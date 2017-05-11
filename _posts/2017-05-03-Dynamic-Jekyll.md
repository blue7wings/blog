---
layout: post
title: "动态使用Jekyll博客"
subtitle: "静态博客有很多好处，轻，巧，但是我不是那么想去每次都编译"
date: 2017-04-01
author: LiamHsia
category: Tutorial
finished: true
---

喜欢折腾的人好像总是「喜新厌旧」的，从Wordpress那种动态博客中逃离出来，用了本地编译mardwon生成文章的Jekyll，总觉得哪里不对，如果不需要每次都编译，直接用markdown写完就能生效该多好啊。

## 解决方案

当然是可以的，想要实现，总是能实现的，是在不行我们还可以造轮子，不过，已经有比较好的解决方案，不用我们非那么大的劲去写代码造轮子了。

![发布博文](http://ooyc2y4k2.bkt.clouddn.com/LLWNZ)

主要依赖两个工具：
1. 代码构建工具 - [Travis-ci](https://travis-ci.org/) (免费)
2. 能够发布到Github的写作软件 - [Wordmark](http://wordmarkapp.com/) (收费) 

主要原理就是，当我们使用Wordmark这款软件，写完文章提交到Github, Travis会自动构建博客，简单点说就是将本地构建放到了云端。

## 步骤
### 添加自动构建脚本
1. 将 [bin](https://github.com/blue7wings/blog/tree/master/bin) 目录完成拷贝到jekyll根目录下，并给可执行权限。
2. 拷贝 [.travis.yml](https://github.com/nielsenramon/kickster/blob/master/snippets/travis/.travis.yml) 文件至jekyll根目录下，并在以下两行中添加自己的信息。

	`
	USERNAME: <your-github-username>
	`

	`
	EMAIL: <your-github-email>
	`
3. 打开Github, 在`settings > Personal access tokens`中，勾选`repo`，创建新的`access token`，并复制。返回至jekyll项目目录，在终端中输入：

	`> gem install travis`
    
	`> travis encrypt GITHUB_TOKEN=复制的token --add`
    
    此操作会在`.travis.yml`中添加一行
    
    `secure: <encrypted token>
    `
4. 在 [Travis CI](https://travis-ci.org/) 中启用博客所在仓库。

### WordMark发布设置
设置 -> 发布
![Untitled Image](http://ooyc2y4k2.bkt.clouddn.com/kvz1A)
文件路径选择 `_post` ，图床不建议勾选，可以使用单独添加七牛的图床，更加方便。

![Untitled Image](http://ooyc2y4k2.bkt.clouddn.com/KO0v0)

### Done
当我们写完一篇文章之后，点击左下角的发布，选择发布的博客，`WordMark` 就会把文章提交到 `_posts` 文件夹下，`Travis` 会为我们重新构建项目，一篇静态的博文就会被**动态**的生成好了

![Untitled Image](http://ooyc2y4k2.bkt.clouddn.com/A7zGw)