---
title: 博客配置记录
date: 2019/12/20 16:19
categories: 
- hexo
tags: hexo
---

## 关于本站

**Powered by:**
- [Hexo](https://hexo.io/zh-cn/): 博客框架
- [Next](https://github.com/theme-next/hexo-theme-next): 基于Hexo的博客主题
- [LeanCloud](https://www.leancloud.cn/): 数据云存储与后端支持
- [Valine](https://valine.js.org/): 文章评论组件

Notes: 目前使用Next 5.1.4版本

## 更新记录

### 2019-12-20

#### 首页文章仅预览

位置：themes/next/_config.yml:219

```yml
# Automatically Excerpt. Not recommend.
# Please use <!-- more --> in the post to control excerpt accurately.
auto_excerpt:
  enable: true
  length: 150
```

Notes: 新版本移除了此变量，将此功能独立于主题，借助第三方实现



####  启用valine评论功能

位置：themes/next/_config.yml:381

[服务提供商‘LeanCloud’](https://leancloud.cn)[国内厂商，现在要实名，你懂得]，[文档](https://juejin.im/post/5d790e706fb9a06af8250665)



#### 部分文章开启或关闭版权和打赏

位于：themes/next/layout/_macro/post.swig:349

```bash
    {############局部文章开启或关闭#############}
    {% if post.reward !== false %}
      {% if (theme.alipay or theme.wechatpay or theme.bitcoin) and not is_index %}
        <div>
          {% include 'reward.swig' %}
        </div>
      {% endif %}
    {% endif %}

    {############局部文章开启或关闭#############}
    {% if post.copyright !== false %}
      {% if theme.post_copyright.enable and not is_index %}
        <div>
          {% include 'my-copyright.swig' with { post: post } %}
        </div>
      {% endif %}
    {% endif %}
```



#### 关闭底部hexo和主题信息

位置：themes/next/_config.yml:45

```yml
footer:
  # -------------------------------------------------------------
  # Hexo link (Powered by Hexo).
  powered: false

  theme:
    # Theme & scheme info link (Theme - NexT.scheme).
    enable: false
    # Version info of NexT after scheme info (vX.X.X).
    version: true
  # -------------------------------------------------------------
```












