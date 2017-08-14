# 如何写webpack2.0插件

## 背景

[直奔主题]()

公司开发的微信公众号Webapp一直都存在缓存问题。但因为本公众号的服务号性质，使用者并不多，且版本迭代较慢等原因，一直没有重视。

近期用户数开始增多，版本迭代也开始加速，该问题变得越来越明显。新特性无法即刻呈现、旧BUG无法及时清除，着实恼人。为了保住饭碗不被饿死，我决定揪出问题原因，一劳永逸解决它。


## 分析问题

实际上此前本项目也一直都有用插件（webpack-md5-hash）打文件指纹，但该插件仅仅对主入口（bundle.js）引用的其他子文件打上文件指纹，对index中的bundle.js则不添加任何版本信息。因此即使我已经在index.html中添加了content="no-cache"，只要bundle.js没有刷新，缓存就会一直存在。

在npm官网搜了下，发现并没有称手的为引用文件添加版本信息的webpack插件。算了还是自己写一个吧。熟悉怎么写以后，如果再有什么定制化需求，也可以自己实现嘛，反正不亏。

## 着手实现

### 一、基本概念

plugin可以将webpack引擎的全部潜力暴露给第三方开发者。通过**阶段构建回调**，开发者可以在webpack构建过程中引入自己的行为。为此开发者需要了解webpack的底层钩子。

在开发插件时webpack为我们提供了两个非常重要的资源compiler和compilation。

+ 简单地说compiler是webpack运行时的一个全局变量。你在其中可以找到大部分你想要的资源，包括options、loaders和plugin。
+ compilation的接口信息包括模块资源的当前状态、已编译资源、发生变化的文件和观察到的依赖的变化。当webpack运行一个中间件，且检测到文件发生变化时，一个新的compilation将会被创建。

开发者通过实现plugin原型链上的apply方法使其变成可实例化的对象。当webpack安装插件时，webpack compiler就会调用apply方法。apply将会得到一个当下compiler的参考对象。一个简单的plugin的结构如下所示：

```javascript
function HelloWorldPlugin(options) {
  // 通过options设置plugin实例
}

HelloWorldPlugin.prototype.apply = function(compiler) {
  compiler.plugin('done', function() {
    console.log('Hello World!'); 
  });
};

module.exports = HelloWorldPlugin;
```

[原文链接](https://github.com/webpack/docs/wiki/How-to-write-a-plugin#compiler-and-compilation)

### 二、webpack生命周期

### 三、收集原材料！

### 四、处理输出结果