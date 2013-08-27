---
翻译地址: [英文原版地址](http://coding.smashingmagazine.com/2012/11/05/writing-fast-memory-efficient-javascript/)

----
<div class="hero-unit">

<em>JavaScript引擎（例如Google的V8，chrome和node的js引擎）是专门为快速执行大型js应用程序设计的。
作为开发人员，如果你关心js的内存使用和性能，你就需要知道浏览器的js引擎在背后都做了些什么。
无论了解V8(chrome), SpiderMonkey(FireFox), Carakan(Opera), Chakra(IE)或者别的什么js引擎，都会对你优化你的应用有帮助.
<br />
<span class="label label-warning">
这并不代表只为一种浏览器去做优化就够了,绝不要那样做。
</span>
</em>
</div>

> _Fast-loading Web sites -- like fast cars -- require the use speciolized tools._

### __首先，你需要问自己几个问题:__

 * 在我的代码中还有那些做的不够高效？
 * 常见的js引擎逗做了那些优化？
 * 引擎有那些不能优化的地方，以及垃圾回收(garbage collector)能否如我们所期望的那样清理内存

编写快速高性能的代码时会遇到很多陷阱，接下来我们将探索如何写出高效高质量的代码。

<div class="hr"></div>
### JavaScript在V8中是如何工作的

> _While it's possible to develop large-scale applications without a thorough understanding of JavaScript engines, any car owner will tell you they've looked under the hood at least once.(原文)_


  __下面简单介绍下chrome的js引擎(v8), v8是由几个核心块组成的:__

  * 基本编译器: 解析你的js代码并在执行前声称native machine code,而不是生成字节码或者简单的解释。这个过程只对代码做了初级的优化还不是高度优化的状态;

  * V8将你的对象(Object)标记到一个对象模型中: 对象在javascript中被描述为有关联的数组,但是在V8中，对象被一个内部的类别系统描述为一个隐藏的类从而优化内部查找;

  * 运行时分析监听系统被执行并标记花费时间较长的函数(标记为"hot function");

  * 一个优化编译器重新优化编译上面花费时间较长的函数('hot function'),_and performs optimizations such as inling(i.e. replacing a function call site with body of the callee)_

  * V8支持优化恢复,优化器再发现优化过度的情况下可以撤销优化;   

  * V8有一整套完整的垃圾回收机制: 了解它是如何工作的跟优化js代码一样重要;

#### 1. 垃圾回收机制

垃圾回收通常是一个内存管理机制,就像一个收集者不断回收长时间不再使用的对象,但是javacript中的垃圾回收无法回收在应用中仍然存在引用的对象。
 
通常很难手动清除对象引用,最简单的方法就是只在需要的地方声明局部变量,声明到起作用的作用域中，当该作用域结束后会自动清除相应的局部变量;

我们在javascript中很难控制垃圾回收,你一定也不想这么做,因为垃圾回收进程是在运行时才起作用的,并且它通常会很聪明的清理掉需要清理的东西。

##### 1.1 去除引用的错误概念

 - delete的作用:
	
	在网上讨论javascript的内存回收总会提到<code>delete</code>,它可以删除对象的属性,一些开发者认为利用它可以删除对象引用
		<span class="label label-warning">请劲量避免使用delete</span>

      	//  Issue Example
      	// 在这个例子中,delete object.o 起到了反面作用
      	// 它修改了object的隐藏属性,使js引擎认为该对象为常规对象，不进行优化，从而降低了优化性
      	var object = { o: 1};
      	delete object.o;    // true
      	object.o    // undefined

	你一定在很多知名的javascript库中看到使用delete，他们主要的目的就是避免在运行时js引擎对该对象进行优化，因此使用delete可以有效的避免引擎的优化；

 - null的作用:
 
	我们也经常错误的理解null的工作,_Setting an object reference to null doesn't 'null' the object_,用<code>object.x = null</code>比<code>delete</code>要好，但是可能不是所有都起作用。
		
      	var object = { x: 1};
      	o = null;
      	o; // null
      	o.x // TypeError

	如果这个引用是最后一个指向该对象的引用,那么这个对象将被垃圾回收。反之则不会被垃圾回收，依然存在于内存中。

 - 另外值得注意的:
 
 	全局变量在页面展示周期中不会被垃圾回收。不管页面打开了多久，运行时全局作用域的对象会一直存在。
	全局对象只有在页面刷新，或者跳到别的页面，或者被关闭的情况下被释放；function作用域下的变量在脱离作用域后被释放。当function结束并且没有其他引用到这个对象的时候，这个对象将被释放。

##### 1.2 经验法则

 让立即处理尽可能早的清理尽可能多的内存对象，不要让不需要的内存长期存在;这些都是自动（automatically）完成的,一下有几点需要注意

 - 正如刚才所说的：更好的先择手动清理引用的方法就是合理使用局部变量（减少全即变量的使用，只在相应的上下文环境中声明相关变量）

  > Cleaner code with less to worry about!

 - 注意在不需要的节点上移除绑定事件，尤其是那些最终一定会被移除的节点；

 - 如果使用本地缓存数据，请注意清除缓存或者利用生命周期机制管理，避免产生大块无用数据的开销浪费

#### 2. FUNCTIONS

 下面我们来看下`FUNCTIONS`,正如刚才所说，垃圾回收就是回收不再使用的作用块内存。  
 _To better illustrate this, here are some examples:

      function foo() {
          var bar = new LargeObject();
          bar.someCall();
      }
      // when fooo return , the object whick bar points to is automaticall available for garbage collection,
      // because there is nothing left that has a reference to it.

      // ccompare this to:

      function foo () {
          var bar = new LargeObject();
          bar.someCall();
          return bar;
      }
      // somewhere else
      var b = foo();
      // we now have a reference to the object which survives the call and persists until the caller assigns something else to b
      // or b goes out of scope

### 3. 闭包

下面是一个基本的闭包模型：

	function sum (x) {
		function sumIt (y) {
			return x + y;
		};
		return sumIt;
	}
	
	// Usage
	var sumA = sum(4);
	var sumB = sumA(3);
	console.log(sumB); // Return 7
	
这个`function`对象在`sum`的上下文环境中创建并且无法被回收，因为一个全局地址引用了它，而且很容易获取，还被`sumA(n)`调用。

来看下一个例子。我们是否能得到`largeStr`?

	var a = function () {
		var largeStr = new Array(1000000).join('x');
		return function () {
			return largeStr;
		};
	}();

是的，你可以通过运行`a()`获得`largeStr`, 所以他不会被回收，那么下面的例子呢？

	var a = function () {
		var smallStr = 'x';
		var largeStr = new Array(1000000).join('x');
		return function (n) {
			return smallStr;
		};
	}();

我们无法再获得`largeStr`了，它被垃圾回收释放了。

### 4. 时钟(Timer)

最严重的内存泄漏就是再循环或者`setTimeout()/setInterval()`里面，但是有非常常见。
考虑下面的例子：

	var myObj = {
    	callMeMaybe: function () {
        	var myRef = this;
        	var val = setTimeout(function () {
            	console.log('Time is running out!');
            	myRef.callMeMaybe();
        	}, 1000);
    	}
	};

如果我们运行：

	1myObj.callMeMaybe();

执行这个`timer`,每秒输出`Time is running out!`,然后我们再运行：

	1myObj = null;

The timer will still fire. `myObj` won’t be garbage collected as the closure passed to setTimeout has to be kept alive in order to be executed. In turn, it holds references to myObj as it captures myRef. This would be the same if we’d passed the closure to any other function, keeping references to it.

这个时钟还会继续输出，`myObj`没有被回收，在循环中缓存了`myObj`的引用,所以无法被回收;

It is also worth keeping in mind that references inside a setTimeout/setInterval call, such as functions, will need to execute and complete before they can be garbage collected.

同样值得注意的是在`setTimeout/setInterval`中执行的引用，例如`functions`，在回收之前需要完整执行;
(这段带上了原文，不太会翻译！)
