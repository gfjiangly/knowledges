不要想着合并tfrecord，可以将多个tfrecord作为一个数据集读取，这样多个分片同时读取也会更快一些。如果是按label打包的话，还会使label分布更均匀。

```python
# Create dataset from multiple .tfrecord files
list_of_tfrecord_files = [dir1, dir2, dir3, dir4]
dataset = tf.data.TFRecordDataset(list_of_tfrecord_files)

# Save dataset to .tfrecord file
filename = 'test.tfrecord'
writer = tf.data.experimental.TFRecordWriter(filename)
writer.write(dataset)
```



分割tfrecord

```python
import tensorflow as tf

def split_tfrecord(tfrecord_path, split_size):
    with tf.Graph().as_default(), tf.Session() as sess:
        ds = tf.data.TFRecordDataset(tfrecord_path).batch(split_size)
        batch = ds.make_one_shot_iterator().get_next()
        part_num = 0
        while True:
            try:
                records = sess.run(batch)
                part_path = tfrecord_path + '.{:03d}'.format(part_num)
                with tf.python_io.TFRecordWriter(part_path) as writer:
                    for record in records:
                        writer.write(record)
                part_num += 1
            except tf.errors.OutOfRangeError: break
```

