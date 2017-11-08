#### 1. 单例模式
- 定义：保证一个类仅有一个实例，并提供一个访问它的全局访问点。
1. 代理单例模式
根据已有类，派生出一个用于生成单例的类。该派生类不论经过几次实例化，都只会返回最初的那个实例化对象。
``` javascript
var CreateDiv = function(html){//原始类
		this.html = html;
		this.init();
};
CreateDiv.prototype.init = function(){
		var div = document.createElement("div");
		div.innerHTML = this.this;
		document.body.appendChild(div);
};
var ProxySingletonCreateDiv = (//派生的代理单例类
		var instance;
		return function(html){
				if(!instance){
						instance = new CreateDiv(html);
				}
				return instance;
		}
);
var a = new ProxySingletonCreateDiv("Helex1");
var b = new ProxySingletonCreateDiv("Helex2");
console.log(a === b);//true
console.log(a.html);//Helex1
```
2. 惰性单例模式
在需要时才创建对象实例。
```javascript
var getSingle = function(fn){
		var result;
		return function(){
				return result || ( result = fn.apply(this, arguments) );
		}
};
var bindEvent = getSingle(function(){
		document.getElementById("div1").onclick = function(){
				alert("click");
		}
		return true;
});
var render = function(){
		console.log("开始渲染列表");
		bindEvent();
};
render();//打印:"开始渲染列表" -> 完成事件绑定
render();//打印:"开始渲染列表" -> 无任何操作
render();//打印:"开始渲染列表" -> 无任何操作
```