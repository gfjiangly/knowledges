

```cpp
// 1. 首先要申请GPU内存（输入和输出）
// 2. 从CPU拷贝boxes数据到GPU，阈值为
// 3. 线程与数据映射，即一个线程要计算哪些数据，共需要多少线程。
// 4. 内核计算
// 5. 从GPU取回结果
// 6.释放GPU内存

// DIVUP即实现除法的向上取整
#define DIVUP(m,n) ((m) / (n) + ((m) % (n) > 0)) 

// dev表示device, 不是开发的意思
CUDA_CHECK(cudaMalloc(&boxes_dev,                        
                      boxes_num * boxes_dim * sizeof(float)));  
CUDA_CHECK(cudaMemcpy(boxes_dev,                        
                      boxes_host,                        
                      boxes_num * boxes_dim * sizeof(float),                
                      cudaMemcpyHostToDevice));   
const int col_blocks = DIVUP(boxes_num, threadsPerBlock);
CUDA_CHECK(cudaMalloc(&mask_dev,                        
                      boxes_num * col_blocks * sizeof(unsigned long long)));


// unsigned long long类型是目前C语言中精度最高的数据类型，为64位精度
// threadsPerBlock即自定义的每个Block所含有的线程数目（每个Block的线程数不宜太多，也不宜太少）
int const threadsPerBlock = sizeof(unsigned long long) * 8; // 其实threadsPerBlock = 64 
dim3 blocks(DIVUP(boxes_num, threadsPerBlock),
            DIVUP(boxes_num, threadsPerBlock));  // 块数，二维
dim3 threads(threadsPerBlock);  // 每个块线程数，一维


nms_kernel<<<blocks, threads>>>(boxes_num,                                  
                                nms_overlap_thresh,                                  
                                boxes_dev,                                  
                                mask_dev);   


std::vector<unsigned long long> mask_host(boxes_num * col_blocks); 
CUDA_CHECK(cudaMemcpy(&mask_host[0],
                      mask_dev,         
                      sizeof(unsigned long long) * boxes_num * col_blocks,  
                      cudaMemcpyDeviceToHost));
```

```c
__global__ void nms_kernel(const int n_boxes, 
                           const float nms_overlap_thresh,                           
                           const float *dev_boxes, 
                           unsigned long long *dev_mask) {
    const int row_start = blockIdx.y;  
    const int col_start = blockIdx.x;   
    // min 是因为有一个块的线程数量 <= threadsPerBlock
    const int row_size = min(n_boxes - row_start * threadsPerBlock, threadsPerBlock);  
    const int col_size = min(n_boxes - col_start * threadsPerBlock, threadsPerBlock); 
    
    // 拷贝线程块对应数据到共享内存，加快访存
    __shared__ float block_boxes[threadsPerBlock * 5];
    if (threadIdx.x < col_size) {    
        block_boxes[threadIdx.x * 5 + 0] = \
            dev_boxes[(threadsPerBlock * col_start + threadIdx.x) * 5 + 0];    
        block_boxes[threadIdx.x * 5 + 1] = \
            dev_boxes[(threadsPerBlock * col_start + threadIdx.x) * 5 + 1];
        block_boxes[threadIdx.x * 5 + 2] = \
            dev_boxes[(threadsPerBlock * col_start + threadIdx.x) * 5 + 2];   
        block_boxes[threadIdx.x * 5 + 3] = \
            dev_boxes[(threadsPerBlock * col_start + threadIdx.x) * 5 + 3];   
        block_boxes[threadIdx.x * 5 + 4] = \
            dev_boxes[(threadsPerBlock * col_start + threadIdx.x) * 5 + 4];  
    }  
    __syncthreads();  // 同步一个块的线程   
    
    // 计算某一框与其余所有框进行交并比的阈值判断
    if (threadIdx.x < row_size) {    
        const int cur_box_idx = threadsPerBlock * row_start + threadIdx.x;    
        // 从全局内存拷贝当前处理的框
        const float *cur_box = dev_boxes + cur_box_idx * 5;    
        int i = 0;    
        unsigned long long t = 0;    
        int start = 0;    
        if (row_start == col_start) {     
            start = threadIdx.x + 1;    // 自己不用和自己计算 
        }    
        for (i = start; i < col_size; i++) {      
            if (devIoU(cur_box, block_boxes + i * 5) > nms_overlap_thresh) {        
                // 用一个64位的t来标记一个大小为64的线程块的计算结果，减少显存使用
                // 1为需要去重的框
                t |= 1ULL << i;  // 1ULL = unsigned long long型的数字1
            }   
        }    
        const int col_blocks = DIVUP(n_boxes, threadsPerBlock);    
        // 存入当前边界框与当前选定的block中的64个边界框的交并比比较情况，在CPU中还要对这个结果进一步处理
        dev_mask[cur_box_idx * col_blocks + col_start] = t;  
    }
}

__device__ inline float devIoU(float const * const a, float const * const b) {  
    float left = max(a[0], b[0]), right = min(a[2], b[2]);  
    float top = max(a[1], b[1]), bottom = min(a[3], b[3]);  
    float width = max(right - left + 1, 0.f), height = max(bottom - top + 1, 0.f);  
    float interS = width * height;  
    float Sa = (a[2] - a[0] + 1) * (a[3] - a[1] + 1);  
    float Sb = (b[2] - b[0] + 1) * (b[3] - b[1] + 1);  
    return interS / (Sa + Sb - interS);
}
```

```cpp
// 对GPU标记的iou结果的后处理，CPU代码
std::vector<unsigned long long> remv(col_blocks); 
// 存储要移除的边界框索引  
memset(&remv[0], 0, sizeof(unsigned long long) * col_blocks);  // 初始化为0   
// 以下正式开始进行nms，思想和CPU版本有所不同，但本质是一样的  
// 由于输入此函数的boxes_host是按置信度从高到低排过序，所以第一个边界框肯定会存入keep_out中  
int num_to_keep = 0;  
for (int i = 0; i < boxes_num; i++) {    
    int nblock = i / threadsPerBlock;  // 当前边界框输入哪一个block    
    int inblock = i % threadsPerBlock; // 当前边界框输入对应block中的第几个     
    // 判断当前边界框与前面保留下来的边界框之间的交并比是否大于阈值    
    // 当i = 0时，条件为true，但由于第一个边界框肯定要存入keep_out中，所以没问题
    // 这种写法比较精炼，也比较难读，正常写法，应是先给remv赋值，然后启动循环，但这里利用了第一次进入后赋值
    if (!(remv[nblock] & (1ULL << inblock))) {   
        keep_out[num_to_keep++] = i;  // 如果不大于阈值，则当前边界框应该保留      
        unsigned long long *p = &mask_host[0] + i * col_blocks;      
        for (int j = nblock; j < col_blocks; j++) {        
            remv[j] |= p[j];  // 预存入后续所有边界框是否要被移除的信息（相应位为1则移除）      
        }    
    }  
}  
*num_out = num_to_keep;
```

