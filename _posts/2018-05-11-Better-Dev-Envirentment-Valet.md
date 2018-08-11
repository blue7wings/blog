---
layout: post
title: "更好的PHP开发环境-Valet篇"
subtitle: "Mac用户最为方便的开发环境"
date: 2018-05-11
author: LiamHsia
category: PHP Tutorial
tags: PHP 开发环境
finished: true
---

## 前言

在前面的文章里，我介绍了[Vagrant](http://www.blue7wings.com/php%20tutorial/Better-Dev-Envirenment-Vagrant.html)和[Docker](http://www.blue7wings.com/php%20tutorial/Better-Dev-Envirenment-Docker.html)这两种开发环境的搭建，归根到底这两种开发环境都是基于虚拟环境的技术来实现的，对于一个Mac用户来说，本身系统就是类Linux的Unix操作系统，差异不大，完全没必要去安装一个虚拟机，在Mac上使用Linux操作系统。而大多数人的痛点就是，我不关心究竟用的是什么技术，只想把项目运行起来，而整个开发环境配置过于麻烦，`Nginx` + `PHP` 这一套从安装到配置就得不少时间。

`Valet`的出现就解决了这一痛点。`Valet`是一套PHP开发套件，基于本地的PHP，`Valet`解决了配置难的问题，一条指令就能完成所有的配置，非常之方便。

## 安装

默认你的Mac电脑上已经安装完`PHP`和`Composer`，使用`Composer`来安装`Valet`：

```
composer global require laravel/valet
```

![](http://ooyc2y4k2.bkt.clouddn.com/2018-08-11-Jietu20180811-171418%402x.jpg)

```
valet install 
```

安装和配置所需要的组件，并且注册开机启动。

![](http://ooyc2y4k2.bkt.clouddn.com/2018-08-11-Jietu20180811-172924%402x.jpg)

如果安装成功，可以发现，`.test`后缀的域名可以Ping通。

![](http://ooyc2y4k2.bkt.clouddn.com/2018-08-11-Jietu20180811-173152%402x.jpg)

## 使用
使用简单是`Valet`的最大的特色，进入我们Laravel项目所在的项目，使用`valet link app-name`来启动这个项目，然后用`valet links`可以查看所有已经启动的项目，和关联的文件目录。

![](http://ooyc2y4k2.bkt.clouddn.com/2018-08-11-Jietu20180811-174549%402x.jpg)

访问`la.test`，可以发现这个项目已经成功部署了。如果想要取消关联此项目，使用`valet unlink app-name`即可。

![](http://ooyc2y4k2.bkt.clouddn.com/2018-08-11-Jietu20180811-174832%402x.jpg)

可以看到，`valet`大大简化了我们配置一个项目的过程，尤其是`Nginx`和`PHP`的配置，只需要使用`valet link`即可完成替我们完成所有的配置项。但需要注意的是，`Valet`现在只支持特定的PHP框架，如果，你使用的框架不再下面的列表中，可以参见[https://laravel.com/docs/5.6/valet#custom-valet-drivers](https://laravel.com/docs/5.6/valet#custom-valet-drivers) 来进行自定义配置。

- Laravel
- Lumen
- Bedrock
- CakePHP 3
- Concrete5
- Contao
- Craft
- Drupal
- Jigsaw
- Joomla
- Katana
- Kirby
- Magento
- OctoberCMS
- Sculpin
- Slim
- Statamic
- Static HTML
- Symfony
- WordPress
- Zend
