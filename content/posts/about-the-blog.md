---
title: "About the Blog"
date: 2022-12-14T22:34:25+08:00
draft: false
categories:
- 杂项
tags:
- hugo
comments:       false
showMeta:       false
showActions:    false
summary: "为什么以及如何构建这个blog"
---

#### 为什么需要blog
1. 人是会遗忘的；此处将人的大脑类比为内存；
2. 如果你完全遗忘了曾经学过或记住的东西，那么过去学习的时间就被浪费了；重新学习类似从网络中下载，是慢的；
3. 如果你总是努力记住太多的东西，学习新知识的速度和能力可能会变慢；内存太满，显然会影响程序的运行；
4. 将学习过的关键知识完善、严谨地记录，能够使得日后需要用但是大脑已经遗忘时，重新学习的速度变快；类似于从SSD中加载到内存；
5. 博客应当有较为系统的组织方式，如categories或tags；类似于数据库中的索引；
6. 相较于本地笔记，因为有被他人读的可能，写出博客一般更正式；
7. 因此，应当大胆地学习、记录、遗忘；


#### 如何用Hugo构建blog
为了专注于Hugo的用法，此处省略了部分git相关操作
构造hugo项目
```bash
hugo new site zzhpro.github.io
```
安装主题
```bash
cd zzhpro.github.io
git submodule add https://github.com/kakawait/hugo-tranquilpeak-theme.git themes/hugo-tranquilpeak-theme
```
编辑配置文件`config.toml`，修改图片、侧边栏排版等；参考[文档](https://github.com/kakawait/hugo-tranquilpeak-theme/blob/master/docs/user.md)
创建新的post
```bash
hugo new posts/about-the-blog.md
```
在生成的markdown文件的开头补充下面的yaml配置，以完善tag及category；注意如果列出了多项category，他们之间是层次化的关系；
```yaml
categories:
- 杂项
tags:
- hugo
```
运行项目
```bash
hugo server
```
注意新生成的博客默认配置中`draft`为`false`，此时他将不会被展示到网页上，更改为`true`即可，或者用debug模式运行
```bash
hugo server -D
```
如果不在posts开头的yaml配置中指定summary，他就会为你自动生成一段，这就会导致home页面很丑，因此建议设置summary。

传统的代码高亮不起作用，需要用新写法：
![p1](https://github.com/zzhpro/zzhpro.github.io/raw/main/static/images/p1.png)
{{< codeblock "codeblockname" "cpp">}}
{{< /codeblock >}}

#### 部署到Githu Pages
不同于Jekyll，Hugo项目无法直接被Github理解，需要用Github Actions来部署；直接在setting/Pages中图形化操作即可；