Travis CI 提供的是持续集成服务（Continuous Integration，简称 CI）。它绑定 Github 上面的项目，只要有新的代码，就会自动抓取。然后，提供一个运行环境，执行测试，完成构建，还能部署到服务器。

持续集成指的是只要代码有变更，就自动运行构建和测试，反馈运行结果。确保符合预期以后，再将新代码"集成"到主干。

持续集成的好处在于，每次代码的小幅变更，就能看到运行结果，从而不断累积小的变更，而不是在开发周期结束时，一下子合并一大块代码。

- 只支持 Github 
-  项目 根目录下 包含构建或测试脚本 .travis.yml

```yml
dist: bionic  # ubuntu 18.04
language: python

python:
  - "3.5"
  - "3.6"
  - "3.7"

# 定义环境变量, 脚本内部使用这些变量
# 有些环境变量（比如用户名和密码）不能公开，这时可以通过 Travis 网站，写在每个仓库的设置页里面，Travis 会自动把它们加入环境变量。这样，脚本内部依然可以使用这些环境变量，但是只有管理员才能看到变量的值。
env: CUDA=10.1.105-1 CUDA_SHORT=10.1 UBUNTU_VERSION=ubuntu1804

cache: pip

# Ref to CUDA installation in Travis: https://github.com/jeremad/cuda-travis
before_install:
  - INSTALLER=cuda-repo-${UBUNTU_VERSION}_${CUDA}_amd64.deb
  - wget http://developer.download.nvidia.com/compute/cuda/repos/${UBUNTU_VERSION}/x86_64/${INSTALLER}
  - sudo dpkg -i ${INSTALLER}
  - wget https://developer.download.nvidia.com/compute/cuda/repos/${UBUNTU_VERSION}/x86_64/7fa2af80.pub
  - sudo apt-key add 7fa2af80.pub
  - sudo apt update -qq
  - sudo apt install -y cuda-${CUDA_SHORT/./-} cuda-cufft-dev-${CUDA_SHORT/./-}
  - sudo apt clean
  - CUDA_HOME=/usr/local/cuda-${CUDA_SHORT}
  - LD_LIBRARY_PATH=${CUDA_HOME}/lib64:${CUDA_HOME}/include:${LD_LIBRARY_PATH}
  - PATH=${CUDA_HOME}/bin:${PATH}

# 如果不需要安装，即跳过安装阶段，就直接设为true。
# install: true
install:
  # 如果某个command失败了，整个构建就会停下来，不再往下进行
  - pip install Cython torch==1.2
  - pip install -r requirements.txt
  - pip install -r tests/requirements.txt

before_script:
  - flake8 .
  - isort -rc --check-only --diff mmdet/ tools/ tests/
  - yapf -r -d --style .style.yapf mmdet/ tools/ tests/

# 用来指定构建或测试脚本
# 注意，script与install不一样，如果command1失败，command2会继续执行。但是，整个构建阶段的状态是失败。如果command2只有在command1成功后才能执行，就要写成: script: command1 && command2
script:
  - python setup.py check -m -s
  - python setup.py build_ext --inplace
  - coverage run --source mmdet -m py.test -v --xdoctest-modules tests mmdet

# or afterfailure
after_success:
  - coverage report

# [OPTIONAL] before_deploy
# [OPTIONAL] deploy
# [OPTIONAL] after_deploy
# after_script
```

Travis 每次运行，可能会返回四种状态。

> - passed：运行成功，所有步骤的退出码都是`0`
> - canceled：用户取消执行
> - errored：`before_install`、`install`、`before_script`有非零退出码，运行会立即停止
> - failed ：`script`有非零状态码 ，会继续运行



参考：

- http://www.ruanyifeng.com/blog/2017/12/travis_ci_tutorial.html
- https://zhuanlan.zhihu.com/p/67805669