---
layout: post
title: fisrt New Post
date: 2025-06-16 22:47 +0800
author: tangyu
pin: true
math: true
mermaid: true
---




## Documentation

To learn how to use, develop, and upgrade the project, please refer to the [Wiki][wiki].



上面的链接仅供参考，

## 部署
建议使用JetBrains进行容器化开发，win11下无法将`_javascript`生成到`assets/js/dist`的mini版本。

确保可以git正常



[https://blog.csdn.net/qiuyeyyy/article/details/136524182](https://blog.csdn.net/qiuyeyyy/article/details/136524182)

```bash
git config --global --add safe.directory "*"
```




最后执行
```bash
bundle exec jekyll serve
```

### chripy的静态资源

主要用于本地调试，根据教程设置为开发模式

[https://github.com/cotes2020/chirpy-static-assets](https://github.com/cotes2020/chirpy-static-assets)


## 替换图标

[https://pansong291.github.io/chirpy-demo-zhCN/posts/customize-the-favicon/](https://pansong291.github.io/chirpy-demo-zhCN/posts/customize-the-favicon/)

图标生产文件

[https://realfavicongenerator.net/](https://realfavicongenerator.net/)


linux与win的换行符自动检测
```bash
git config --global core.autocrlf tru
```

## 插件安装

### 1、jekyll compose

Jekyll Compose 是一个用于简化 Jekyll 写作流程的插件。通过提供一系列命令，如创建草稿、发布文章等，Jekyll Compose 使得在 Jekyll 中管理内容变得更加高效和便捷。

[https://github.com/jekyll/jekyll-compose](https://github.com/jekyll/jekyll-compose)

在`./Gemfile`文件中添加
```bash
gem 'jekyll-compose', group: [:jekyll_plugins]
```
安装并查看
```bash
bundle install
bundle exec jekyll help
```


创建新页面
```bash
bundle exec jekyll page "My New Page"
```

创建新页面
```bash
bundle exec jekyll post "My New Post"
# or specify a custom format for the date attribute in the yaml front matter
bundle exec jekyll post "My New Post" --timestamp-format "%Y-%m-%d %H:%M:%S %z"
```
为 draft 和 posts 设置默认 front matter

如果你想为新创建的帖子或草稿添加默认的 front matter，你可以在 config keys 下指定任意数量的内容，例如：default_front_matter

```yaml
jekyll_compose:
  default_front_matter:
    drafts:
      description:
      image:
      category:
      tags:
    posts:
      description: demo描述
      image:
      category: 默认类别
      tags: 默认标签
      published: false
      sitemap: false
```
### 2.添加评论插件gicus
giscus通过GitHubDiscussions实现社区化讨论，简化用户注册，提供实时同步和定制选项。

[https://giscus.app/zh-CN](https://giscus.app/zh-CN)

[https://blog.csdn.net/sinat_41212418/article/details/137819858](https://blog.csdn.net/sinat_41212418/article/details/137819858)


### 3.站点统计goatcounter

[https://tangyu.goatcounter.com/](https://tangyu.goatcounter.com/)
```yaml
analytics:
  google:
    id: # fill in your Google Analytics ID
  goatcounter:
    id: tangyu # fill in your GoatCounter ID
  umami:
    id: # fill in your Umami ID
    domain: # fill in your Umami domain
  matomo:
    id: # fill in your Maatomo ID
    domain: # fill in your Matomo domain
  cloudflare:
    id: # fill in your Cloudflare Web Analytics token
  fathom:
    id: # fill in your Fathom Site ID

# Page views settings
pageviews:
  provider: goatcounter # now only supports 'goatcounter'
```
设置完成后看看生成的site是否有goatcounter链接






## bug修复

```bash

Conversion error: Jekyll::Converters::Scss encountered an error while converting 'assets/css/jekyll-theme-chirpy.scss

```
记得资源换成production
