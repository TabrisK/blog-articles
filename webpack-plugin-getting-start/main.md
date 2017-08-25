## 背景

[直奔主题](#main)

公司开发的微信公众号Webapp一直都存在缓存问题。但因为公众号的服务号性质，使用者并不多，且版本迭代较慢等原因，一直没有重视。

近期用户数开始增多，版本迭代也开始加速，该问题变得越来越明显。新特性无法即刻呈现、旧BUG无法及时清除，着实恼人。为了保住饭碗不被饿死，我决定揪出问题原因，一劳永逸解决它。


## 分析问题

实际上此前本项目也一直都有用插件（webpack-md5-hash）打文件指纹，但该插件仅仅对主入口（bundle.js）引用的其他子文件打上文件指纹，对index中的bundle.js则不添加任何版本信息。因此即使我已经在index.html中添加了content="no-cache"，只要bundle.js没有刷新，缓存就会一直存在。

在npm官网搜了下，发现并没有称手的为引用文件添加版本信息的webpack插件。算了还是自己写一个吧。熟悉怎么写以后，如果再有什么定制化需求，也可以自己实现嘛，反正不亏。

## <a name="main"></a>着手实现

### 一、基本概念

plugin可以将webpack引擎的全部潜力暴露给第三方开发者。通过**阶段构建回调**，开发者可以在webpack构建过程中引入自己的行为。为此开发者需要了解webpack的底层钩子。

在开发插件时webpack为我们提供了两个非常重要的资源compiler和compilation。

+ 简单地说compiler是webpack运行时的一个全局变量。你在其中可以找到大部分你想要的资源，包括options、loaders和plugin。
+ compilation的接口信息包括模块资源的当前状态、已编译资源、发生变化的文件和观察到的依赖的变化。当webpack运行一个中间件，且检测到文件发生变化时，一个新的compilation将会被创建。

开发者通过实现plugin原型链上的apply方法使其变成可实例化的对象。当webpack安装插件时，webpack compiler就会调用apply方法。apply将会得到一个当下compiler的参考对象。一个简单的plugin的结构如下所示：

#### 例子1

*./hello-world.js*

```javascript
function HelloWorldPlugin(options) {
  // 通过options设置plugin实例
}

HelloWorldPlugin.prototype.apply = function(compiler) {
  compiler.plugin('done', function(satas) {//一切完成后的构建回调
    console.log('Hello World!'); 
  });
};

module.exports = HelloWorldPlugin;
```

接下来只需要在webpack.config中的plugins里引入上面plugin的实例化对象即可。

*./webpack.config.js*

```javascript
var HelloWorldPlugin = require('hello-world');

var webpackConfig = {
  // ... config settings here ...
  plugins: [
    new HelloWorldPlugin({options: true})
  ]
};
```

*./hello-world.js*中，有一个名为"done"的构建回调，所有任务完成后它将被触发。实际上该构建回调还有个参数——Stats，其中记录了最终版的compilation、hash、startTime、endTime信息。

#### <a name="e2"></a>例子2

接下来让我们利用compilation为输出资源添加结果。该例子将设计一个plugin用于生成一份编译结果清单，文件名为filelist.md。

*./file-list-plugin*
```javascript
function FileListPlugin(options) {}

FileListPlugin.prototype.apply = function(compiler) {
  compiler.plugin('emit', function(compilation, callback) {
    // 为生成文件添加头信息:
    var filelist = 'In this build:\n\n';

    // 遍历所有的已编译资源,
    // 并将每个资源文件列为一行.
    for (var filename in compilation.assets) {
      filelist += ('- '+ filename +'\n');
    }
    
    //将这份表单作为一个新的文件资源插入webpack构建:
    compilation.assets['filelist.md'] = {
      source: function() {
        return filelist;
      },
      size: function() {
        return filelist.length;
      }
    };

    callback();
  });
};

module.exports = FileListPlugin;
```

构建回调"emit"在compiler产生(emit)生成资源前被触发。此时插件将有最后的机会添加资源文件到compilation.assets中。你可以在[这里](https://webpack.js.org/api/plugins/compiler/#event-hooks)了解compiler一共提供了哪些构建回调。

[原文链接](https://github.com/webpack/docs/wiki/How-to-write-a-plugin#compiler-and-compilation)

### 二、编写replace-webpack-plugin

本插件和[例子2](#e2)一样在emit构建回调中进行相关逻辑的处理。

```javascript
let opts = {};

function replaceWebpackPlugin(options) {
    opts = arguments.length <= 0 || arguments[0] === undefined ? {} : arguments[0];
}

replaceWebpackPlugin.prototype.apply = function (compiler) {

    compiler.plugin("emit", function (compilation, cb) {
            let allHash = "";//用于记录所有资源hash
            compilation.chunks.forEach(chunk => {//迭代所有chunk
                allHash += chunk.hash;//记录每个chunk的hash
            });
    
            //allHash过长，故对其再次进行md5计算，缩短长度
            allHash = md5(allHash);
    
            //读取需要替换内容的模板
            let template = fs.readFileSync(opts.template, {
                encoding: "utf8"
            });
            //替换模板中的"___fv___"
            template = template.replace("___fv___", allHash);
            //将替换成功后的新模板插入webpack构建:
            compilation.assets['index.html'] = {
                source: function() {
                    return template;
                },
                size: function() {
                    return template.length;
                }
            };
            cb();
        });
}
```