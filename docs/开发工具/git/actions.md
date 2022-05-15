action是github于今年刚推出的功能，提供持续集成服务。实际上action分为两个部分，一部分是编写action脚本，另一部分是使用action脚本。github官方给出了几种常用的action，能满足大部分需求，此外还有一些开发者开发了一些action可供使用，因此实际需要自己编写action的环境和需求较少，一般都是使用别人写好的action。

下面给出使用action进行Python代码检查、包安装以及测试

```yml
name: Python package

on: 
  push:
    branches: 
    - master
    - dev

jobs:
  build:	# job_id

    runs-on: ${{ matrix.os }}  # 使用构建矩阵则不能硬编码
    strategy:
      max-parallel: 4
      matrix:
        os: [ubuntu-latest, windows-latest]
        python-version: [3.5, 3.6, 3.7]

    steps:
    - uses: actions/checkout@v1	# 使用 v1 稳定版本
    # 安装Python环境
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v1
      with:
        python-version: ${{ matrix.python-version }}
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    # 代码规范检查
    - name: Lint with flake8
      run: |
        pip install flake8
        # stop the build if there are Python syntax errors or undefined names
        flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
        # exit-zero treats all errors as warnings. The GitHub editor is 127 chars wide
        flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics
    # 测试
    - name: Test with pytest
      run: |
        pip install pytest
        pytest

```



常用的action功能有

- 抓取代码
- 运行测试
- 发布到第三方服务 



参考

- https://github.blog/2019-08-08-github-actions-now-supports-ci-cd/
- https://help.github.com/cn/actions/automating-your-workflow-with-github-actions/workflow-syntax-for-github-actions#example-restricting-the-workflow-run-to-specific-branches-and-paths
- http://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html

