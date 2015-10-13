## 阿里云 ubuntu 系统部署 meteor 环境

阿里云服务器买回来是没有安装 curl 这个工具的，需要我们自己手动安装。你可以到 [curl官网](http://curl.haxx.se/download.html) 下载源码包安装，也可以使用本文提供的简单的 apt-get 方法安装。

~~~ bash
$ sudo apt-get update
$ sudo apt-get install curl
$ curl https://install.meteor.com/ | sh
~~~

上面命令执行完成后开始安装 meteor

~~~ bash
$ curl https://install.meteor.com/ | sh
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  6121    0  6121    0     0   3507      0 --:--:--  0:00:01 --:--:--  3507
Downloading Meteor distribution
######################################################################## 100.0%

Meteor 1.2.0.2 has been installed in your home directory (~/.meteor).
Writing a launcher script to /usr/local/bin/meteor for your convenience.
This may prompt for your password.
sudo: unable to resolve host aliyun

To get started fast:

  $ meteor create ~/my_cool_app
  $ cd ~/my_cool_app
  $ meteor

Or see the docs at:

  docs.meteor.com
~~~

看到如上提示就是已经下载并部署完成了，我们来创建一个 meteor example 测试一下是否可以正常访问了。

~~~ bash
$ meteor create --example todos
Created a new Meteor app in 'todos'.

To run your new app:
  cd todos
  meteor

If you are new to Meteor, try some of the learning resources here:
  https://www.meteor.com/learn
~~~

上面是创建了一个 meteor 内置的示例，名字叫 todos，创建完成后进入 todos 文件夹运行 meteor 命令。

~~~ bash
$ cd todos
$ meteor
[[[[[ ~/project/todos ]]]]]

=> Started proxy.
=> Started MongoDB.
=> Started your app.

=> App running at: http://localhost:3000/
~~~

运行 meteor 命令后提示应用程序运行在 http://localhost:3000/ 可以访问试一下吧。
