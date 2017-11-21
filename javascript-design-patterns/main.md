#### 1. 单例模式
- 定义：保证一个类仅有一个实例，并提供一个访问它的全局访问点。
1.1 代理单例模式
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
var ProxySingletonCreateDiv = (function(){//派生的代理单例类
		var instance;
		return function(html){
				if(!instance){
						instance = new CreateDiv(html);
				}
				return instance;
		}
})();
var a = new ProxySingletonCreateDiv("Helex1");
var b = new ProxySingletonCreateDiv("Helex2");
console.log(a === b);//true
console.log(a.html);//Helex1
```
1.2 惰性单例模式
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

#### 2. 策略模式
- 定义：定义一系列的算法，把他们一个个封装起来，并且使它们可以相互替换。

#### 3.代理模式
- 定义：为其他对象提供一种代理，以控制对这个对象的访问。
#### 4.迭代器模式
- 定义：提供一种方法顺序访问一个局和对象中的各个元素，而又不需要暴露该对象的内部表示。
#### 5.发布-订阅模式（观察者模式）
- 定义：当一个对象的状态发生改变，所有依赖于它的对象都将得到通知。
#### 6.命令模式
- 定义：为了能更好的应对“记录、撤销/重做、事务”等意外情况，将一组行为抽象成对象，使之解耦。
#### 7.组合模式
- 定义：将对象组合成树形结构，以表示“部分-整体”的层次结构。
#### 8.模板方法模式
- 定义：定义一个操作中的算法的骨架，而将步骤延迟到子类中。模板方法使得子类可以不改变一个算法的结构即可重定义算法的某些特定步骤。