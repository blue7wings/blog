---
layout: post
title: "Swoole如何实现热加载(autoload)"
date: 2018-06-13
author: LiamHsia
category: Swoole
finished: true
---

Swoole的确是一款非常优秀的PHP框架，在性能上给予了PHP质的飞跃，在将部分业务逻辑嫁接到Swoole上之后，性能就提升了将近4倍，这还仅仅是没有使用异步等操作的情况下，如果有兴趣，可以查看[对比视频](https://www.youtube.com/watch?v=IKF5IdBPlWY)。

![](http://ooyc2y4k2.bkt.clouddn.com/2018-06-13-%E6%88%AA%E5%9B%BE%202018-06-13%2016%E6%97%B609%E5%88%8622%E7%A7%92.png)
但是Swoole现有的文档非常之匮乏，你可以把现有的文档看成API文档，而不是面向新手的教程，在使用Swoole的过程中，遇到的第一个问题，Swoole如何才能热加载呢？每次修改完代码还需要关闭服务然后重启，才能让新的文件加载进去，非常之不友好，在接下来的的内容中，便会解决这个问题。

## Swoole的加载机制
Swoole性能之所以如此优秀，很大原因是改变了以往每次请求便加载一遍文件的模式，而改成在服务器启动的时候统一加载所有的文件，在每次请求的时候不会进行再次加载和初始化，从而大大减少了请求的消耗。如果你已经了解一点Swoole的实现，可以知道Swoole在启动的时候，Manager进程会创建N个Worker进程来接受/发送数据，实现业务逻辑，当Worker进程因为致命错误而关闭，Manager进程会重新创建，便会重新加载文件。

## 热加载实现
在实现热加载之前我们先看一看代码，主文件`index.php`，加载的文件`greet.php`。

```php
<?php
use Swoole\Http\Server;
use Swoole\Http\Request;
use Swoole\Http\Response;

$http = new Server("127.0.0.1", 9501);

$http->on("request", function (Request $request, Response $response) {
    include_once 'greet.php';
    $response->header("Content-Type", "text/plain");
    $response->end(hello());
});

$http->start();
```

```php
<?php
    function hello(){
        return "Hello";
    }
```

请注意` include_once 'greet.php';`这句并没有像我们以前写PHP代码放在文件的第一句，而是放在`$http->on()`的回调函数中，这是因为Worker进程因为致命错误而重启，但是Manager进程可不会，如果放在代码第一行，就表示只有Manager进程重启才会生效，这显然不是我们需要的。

知道上面的加载机制，我们就可以想办法让Worker进程重启。

### 方法一
向Master进程发送`USR1信号`，`USR1信号`通过Manager进程转发给Worker进程，Worker进程便会重启，我们如下实现：

```
kill -USR1 master_pid 
```

查看Swoole的进程信息：
![](http://ooyc2y4k2.bkt.clouddn.com/2018-06-13-%E6%88%AA%E5%9B%BE%202018-06-13%2017%E6%97%B607%E5%88%8629%E7%A7%92.png)

`30322`便是我们的主进程ID，`kill -USR1 30322` Woker进程会关闭，并被Manager重启，通过下面的图可以看到，Worker进程已经改变，但主进程进程却没有任何变化。
![](http://ooyc2y4k2.bkt.clouddn.com/2018-06-13-%E6%88%AA%E5%9B%BE%202018-06-13%2017%E6%97%B610%E5%88%8604%E7%A7%92.png)

每次在`greet.php`中修改完代码，用`kill -USR1 30322` 重启Worker进程，代码便会重新加载进去。这种方法缺陷也很明显，需要手动去给主进程发送信号，如果想要实现监听文件改动，自动发送信号，还需要继续编写代码，倒是有点得不偿失了。

### 方法二
可以同配置`max_request`来控制Worker进程的最大任务数。

> 一个Worker进程在处理完超过此数值的任务后将自动退出，进程退出后会释放所有内存和资源。

这也变向地实现了Worker进程的自动重启，给`max_request`设置值为1，也就是说当一次请求之后，Worker进程便会重启。

修改`index.php`如下：

```php
<?php
use Swoole\Http\Server;
use Swoole\Http\Request;
use Swoole\Http\Response;

$http = new Server("127.0.0.1", 9501);

$http->set([
    'max_request' => 1
]);

$http->on("request", function (Request $request, Response $response) {
    include_once 'greet.php';
    $response->header("Content-Type", "text/plain");
    $response->end(hello());
});

$http->start();
```

修改`greet.php`的内容，重新刷新页面，内容已经改变，我们的热加载功能也实现了。

## 参考
[Swoole编程指南-2.6 热加载](http://www.catplanet.me/?id=10)
[max_request](https://wiki.swoole.com/wiki/page/300.html)