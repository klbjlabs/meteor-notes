## Meteor 发布与订阅

Meteor 对于数据的处理与传统的 web 应用不同的是，传统的 web 应用将数据在后台从数据库中读取出来，然后由MVC中得 Controller 对数据进行过滤，然后由 View 对数据包装成前端可以识别的数据格式前端显示。而 Meteor 后台从数据库中获取数据可以根据客户端传递过来的参数获取数据，并且可以设定过滤条件，相当于 MVC 模式中的 Controller，把获取到的数据通过发布（publish）功能将数据发布出去，客户端如果需要获得服务端发布出来的数据就需要订阅（subscribe），订阅后，这部分数据会保存在客户端的一个 MiniMongo 数据库中，这个 MiniMongo 存多少数据完全取决于服务端发布出来多少数据（服务端可以设定过滤条件也可以把所有数据都发布出来）。当需要把数据显示出来的时候，客户端模板的 helper 就可以使用 js 代码对本地的 MiniMongo 数据库（注意，此时客户端操作的已经是自己本地浏览器中的数据了）的数据再次进行过滤，过滤完成后给模板已经占位好的变量赋值了。整体的发布与订阅流程就是如此。

资料来自：http://zh.discovermeteor.com/chapters/publications-and-subscriptions/

## 服务端发布数据

服务端发布数据的几种方式代码，在server目录创建一个发布的 js 文件，比如叫 publications.js

* 发布全部数据，明显不怎么靠谱
```
Meteor.publish('posts', function() {
    // 服务端将数据库中所有数据发布出去，发布的标签就是 posts，客户端订阅也根据这个发布的名字来订阅
    return Posts.find();
});
```

* 发布一部分数据（相当于 select * from posts where author = ‘Coleman’）
```
Meteor.publish('posts', function() {
    // 检索 author 字段为 Coleman 的数据
    return Posts.find( { 'author': 'Tom Coleman' } );
});
```

* 根据客户端传递过来的数据发布相应的内容，客户端需要在订阅的时候传递参数
```
Meteor.publish('posts', function(author) {
    return Posts.find({author: author});
});
```

* 发布数据中得某些字段并增加过滤条件（相当于 select auther, url from posts where author = 'Coleman'）
```
Meteor.publish('posts', function(){
    return Posts.find({'author': 'Tom Coleman'}, {fields: {
        // 过滤 date 字段
        date: false
    }});
});
```

## 客户端订阅数据

客户端订阅服务端所发布的数据的代码一般定义在客户端根目录下 main.js 中

* 普通的订阅
```
Meteor.subscribe('posts');
```

* 带参数的订阅，对应上面第三种发布方式
```
Meteor.subscribe('posts', 'Tom Coleman');
```

## 客户端展示数据

* 遍历所有已经订阅的数据
```
Template.postsList.helpers({
    posts: function() {
        // 订阅数据后会产生本地数据，在此可以遍历本地数据
        return Posts.find();
    }
});
```

* 可以对数据集再次过滤，我们只是做一个示例，假设我们要过滤 auther 字段为 bob-smith 且 category 字段为 JavaScript 的数据
```
Template.posts.helpers({
    posts: function(){
         // 过滤 author = bob-smith 并且 category = JavaScript 的数据
         return Posts.find({author: 'bob-smith', category: 'JavaScript'});
    }
});
```
