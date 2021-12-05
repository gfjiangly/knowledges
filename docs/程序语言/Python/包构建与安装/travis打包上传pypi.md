1 pypi的密码加密问题

一般pypi的密码或token需要加密，travis.org以及travis.com所用加密算法不同，需要根据github上使用的构建网站是travis.org还是travis.com决定加密时要不要加--pro

如果账号密码验证错误，会产生如下提示

```
# see: https://travis-ci.com/gfjiangly/cvtools/jobs/267266221
HTTPError: 403 Client Error: Invalid or non-existent authentication information. for url: https://upload.pypi.org/legacy/
```



2 pypi已存在的的文件不会被覆盖，并且如果删除release也无法上传此版本，因此一定要先在test.pypi.org上测试构建的包没有问题时才使用travis自动构建上传。

比如有时构建的包缺少.h文件（如果使用了扩展并且没有使用MANIFEST.in包含头文件），如果被传到PyPI，就默认有人使用了此包，就不允许更新此包了，但实际上这个包是坏的，不能被安装成功的，因此传到pypi应该谨慎。

```
# 传到pypi的文件无法在此版本上被更新，需谨慎
# https://travis-ci.com/gfjiangly/cvtools/jobs/267357838
 Skipping cvtoolss-0.0.3a0.tar.gz because it appears to already exist
```

