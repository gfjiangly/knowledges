当建立一个 [source distribution](https://www.osgeo.cn/python-packaging/glossary.html#term-source-distribution-or-sdist) ，默认情况下只包括一组最少的文件。您可能会发现自己希望在源分发中包含额外的文件，例如作者/贡献者文件，以及 `docs/` 目录，或用于测试的数据文件目录。甚至可能有额外的文件 *need* 包括；例如，如果 `setup.py` 计算项目的 `long_description` 通过同时读取自述文件和changelog文件，您将需要在sdist中包含这两个文件，以便从sdist构建或安装文件的人员获得正确的结果。

在源分发中添加和删除文件通过写入 `MANIFEST.in` 文件位于项目根目录。

参考：

- https://www.osgeo.cn/python-packaging/guides/using-manifest-in.html

