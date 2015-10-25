## Meteor Package 制作

[官方介绍](http://docs.meteor.com/#/full/packagejs)

[DiscoverMeteor教程](http://zh.discovermeteor.com/chapters/creating-a-meteor-package/)

## Package 的查找和使用

* 查找自己需要使用的包

  1. 在 atmospherejs 搜索
  2. 在 github 上搜索


* meteor add 安装

  你的项目如果需要某个 meteor 包，那么需要打开项目目录，在项目目录下执行如下命令，即可添加你需要的包，其中 packageName 是你需要的包名字。

  ~~~ bash
  $ meteor add packageName
  ~~~

## 创建自定义 Package

* 注册 [atmospherejs.com](https://atmospherejs.com/) 账号

  在制作 meteor 自定义包之前，你首先要注册一个 atmospherejs 的账号，这个账号与 meteor 官方账号是通用的。这样你才能把你的包发布到网络上，自己或者他人才能使用 meteor add 方式添加你的包到项目中。

* 登陆 atomspherejs 账号

  注册完 atmospherejs 的账号后，你需要在终端下登陆你的账号，这样创建包时程序才知道你提交的包归属与哪个作者。

  ~~~ bash
  $ meteor login     
  Username: mycode
  Password:                                     
  Logged in as mycode. Thinks...
  ~~~

* 创建 meteor 自定义包

  我们做一个案例，来演示 meteor package 的制作过程，这个案例（自定义包）是一个单独的文章提交和查看的页面，我们希望所有使用了这个包的项目可以通过访问 http://url/posts 路径时跳转到我们制作的页面，可以访问 http://url/post/new 来创建一篇文章。需要实现这些功能，我们可以看出，一定是需要用到路由（Router）、模板（Template）和集合 （MiniMongo）这些功能的，我们在设计包的时候也需要设定这个项目所需的依赖包，这一点我们后面再介绍。

  - *创建一个项目*

    一切从头开始，首先创建一个新的 meteor 包，包的名字叫 mycode:posts（当你上传到网络上以后，其他人 meteor add 的时候就使用这个名字）
    ~~~ bash
    $ meteor create --package mycode:posts
    mycode:posts: created in posts
    ~~~
    创建完成后，你可以的到一个 posts 文件夹，进入目录后可以看到 meteor 帮我们自动创建了几个文件
    ~~~ bash
    total 24
    -rw-r--r--  1 myCode  staff    0 10 23 12:14 README.md
    -rw-r--r--  1 myCode  staff  646 10 23 12:14 package.js
    -rw-r--r--  1 myCode  staff  121 10 23 12:14 posts-tests.js
    -rw-r--r--  1 myCode  staff   33 10 23 12:14 posts.js
    ~~~
    其中对我们有用的，只有 package.js，另外两个文件我们直接删除。

  - *编写包的代码*

    包的代码我们已经写完了，为了演示节约时间，我们就直接拷贝原来的代码到包的文件夹中，你可以理解包就是一个 meteor 项目，只不过它被用 meteor add 的方式添加到另外一个项目中作为一个组件来使用。拷贝完成后，包的目录结构如下所示：
    ~~~ bash
    .
    ├── README.md
    ├── client
    │   ├── posts
    │   │   ├── post_edit.html
    │   │   ├── post_edit.js
    │   │   ├── post_new.html
    │   │   ├── post_new.js
    │   │   ├── post_show.html
    │   │   ├── post_show.js
    │   │   ├── posts.html
    │   │   ├── posts_index.html
    │   │   └── posts_index.js
    │   └── routes.js
    ├── lib
    │   └── collections.js
    ├── package.js
    └── server
        └── publications.js

    ~~~
    其中，除了 `package.js` 和 `README.md` 是使用 meteor create 命令生成出来的以外，其他文件都是我们自定义添加的。

  - *修改 package.js 文件*

    package.js 包含了你对自定义包的描述信息。你可以设定这个包的名字、github 地址、所依赖的其他包、以及最重要的包里面都有什么文件。我们首先来看对于包的信息描述的段落：

    ```
    Package.describe({
      // 包的名字，你使用 meteor create --package 命令创建时后面的名字。
      name: 'mycode:posts',
      // 包的版本，每一次更新都需要修改一下包的版本。
      version: '0.0.1',
      // 用一句话描述你包的作用
      summary: '',
      // 包在 github 上的地址
      git: '',
      // 包的文档，指定为包目录下的 README.md，这个文件如果是空的，在发布的时候会报错，所以一定要写上内容
      documentation: 'README.md'
    });
    ```

    以上是对包信息的设定，下面这个 Package.onUse 的段落就是设定包需要包含什么内容了。

    ```
    Package.onUse(function(api) {
      // 这个包要在 meteor 1.2.0.2 以上版本才能使用，根据自己的情况如实填写就可以了
      api.versionsFrom('1.2.0.2');
      // 这个包所依赖的其他包
      api.use('ecmascript');
      // 这个包里面所包含的文件
      api.addFiles('posts.js');
    });
    ```

    注意第一个 `api.versionFrom`，如果你 meteor 项目版本低于这里设定的值，那么这个包是无法正常使用的。查看你当前 meteor 项目的版本可以在项目目录下执行如下命令：

    ~~~ bash
    $ cat .meteor/release
    ~~~

    如果 meteor 项目低于包的版本，你又希望使用这个包，那么可以升级你当前 meteor 项目到最新版本，在项目目录下执行如下命令：

    ~~~ bash
    $ meteor update
    ~~~

    根据我们包的信息，我们修改了自己的 package.js 文件，修改完整后内容如下：

    ```
    Package.describe({
      name: 'mycode:posts',
      version: '0.0.1',
      // Brief, one-line summary of the package.
      summary: 'Maodou.io posts package, A meteor package that provides posts pages at /posts',
      // URL to the Git repository containing the source code for this package.
      git: 'https://github.com/maodouio/meteor-posts',
      // By default, Meteor will default to using README.md for documentation.
      // To avoid submitting documentation, set this field to null.
      documentation: 'README.md'
    });

    Package.onUse(function(api) {
      // version
      api.versionsFrom('1.2.0.2');
      // package
      api.use('iron:router@1.0.12', ["server", "client"]);
      api.use('aldeed:autoform@5.1.2', ["server", "client"]);
      api.use('aldeed:collection2@2.3.3', ["server", "client"]);
      api.use('aldeed:simple-schema@1.3.2', ["server", "client"]);
      api.use('matb33:collection-hooks@0.8.0', ["server", "client"]);
      api.use('twbs:bootstrap@3.3.4', ["server", "client"]);

      // mongo and template
      // https://zh.discovermeteor.com/chapters/creating-a-meteor-package/
      api.use(['minimongo', 'mongo-livedata', 'templating'], 'client');

      // file
      // ls -l | awk '{print "api.addFiles(\""$9"\", \"client\");"}'
      api.addFiles('client/routes.js', 'client');
      api.addFiles("client/posts/post_edit.html", "client");
      api.addFiles("client/posts/post_edit.js", "client");
      api.addFiles("client/posts/post_new.html", "client");
      api.addFiles("client/posts/post_new.js", "client");
      api.addFiles("client/posts/post_show.html", "client");
      api.addFiles("client/posts/post_show.js", "client");
      api.addFiles("client/posts/posts.html", "client");
      api.addFiles("client/posts/posts_index.html", "client");
      api.addFiles("client/posts/posts_index.js", "client");

      api.addFiles("lib/collections.js");

      api.addFiles("server/publications.js", "server");

      // export database
      api.export('Posts');
    });

    ```

## 测试自定义 Package

* 将包的整个文件夹放到项目的 packages 目录下，然后使用命令 meteor add packageName 添加包到项目中，项目会优先到本地的 packages 目录搜索包添加。

## 提交 Package 到 atmospherejs

* 当测试包没有问题后，你可以在包的目录下使用如下命令将包 push 到[atmospherejs.com](https://atmospherejs.com/)

  ~~~ bash
  $ meteor publish --create
  ~~~

  既然已经将包发布到网络上，那么可以移除本地包的文件来添加刚刚我们自己添加的网络包来做测试了。

  ~~~ bash
  $ mv packages/packageDirectory
  $ meteor add packageName
  ~~~

## 更新已提交过的 Package

* 修改包源码

  对你包的关键文件进行修改。

* 修改包版本

  修改包的内容后，如果你希望曾经添加了你包的项目在更新时能获取到你修改后的包，那么你就要对你的包版本进行修改，上一次我们设定的版本是 0.0.1。升级后我们可以增加一个版本，用以区别升级。

  ```
  Package.describe({
    name: 'mycode:posts',
    version: '0.0.2',
    // Brief, one-line summary of the package.
    summary: 'Maodou.io posts package, A meteor package that provides posts pages at /posts',
    // URL to the Git repository containing the source code for this package.
    git: 'https://github.com/maodouio/meteor-posts',
    // By default, Meteor will default to using README.md for documentation.
    // To avoid submitting documentation, set this field to null.
    documentation: 'README.md'
  });
  ```

* 重新推送新更新后的包到网络上

  这次，就不需要我们再次重新创建包了，而是在原有包的基础上再 publish 一次就可以了。

  ~~~ bash
  $ meteor publish
  ~~~

* 更新项目中已经添加的老版本包

  - 第一种方法，重新添加，这种方法可以根据需求
  ~~~ bash
  // 移除包
  $ meteor remove oldPackageName
  // 重新添加
  $ meteor add newPackageName
  ~~~

  - 第二种方法，直接更新，但是这样会把项目用到的所有的包都更新一次，如果有需求使用某个包的老版本的，那么这个命令就不适用了。
  ~~~ bash
  // 直接更新
  meteor update --packages-only
  ~~~
