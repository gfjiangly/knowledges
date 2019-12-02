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
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v1
      with:
        python-version: ${{ matrix.python-version }}
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt

```

作用

- CI/CD
- 上传到PyPi
- 同步文档

参考：

- https://github.blog/2019-08-08-github-actions-now-supports-ci-cd/
- https://help.github.com/cn/actions/automating-your-workflow-with-github-actions/workflow-syntax-for-github-actions#example-restricting-the-workflow-run-to-specific-branches-and-paths

