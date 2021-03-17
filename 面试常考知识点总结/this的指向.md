#### 一、如何改变this的指向

1. 通过call()和apply()
2. 通过bind()方法
3. 通过new一个对象
4. 通过return返回

#### 二、如何判断this的指向

在使用 this 时，为了避坑，你要谨记以下三点：

1. 当函数作为对象的方法调用时，函数中的 this 就是该对象；
2. 当函数被正常调用时，**在严格模式下，this 值是 undefined**，非严格模式下 this 指向的是全局对象 window；
3. 嵌套函数中的 this 不会继承外层函数的 this 值。

我们还提了一下箭头函数，因为箭头函数没有自己的执行上下文，所以箭头函数的 this 就是它外层函数的 this。

1. 直接使用的函数this指向window

   ```js
   function a(){
   	var user= "呵呵"，
       console.log(this.user),
       console.log(this),
   }
   a();
   // undefined
   // window
   ```

2. 谁调用的函数this就指向谁

   ```js
   function o(){
   	var user = "呵呵"，
       fn：function(){
       	console.log(this.user);
       }
   }
   o.fn()
   // 呵呵
   ```

3. 当函数作为对象的方法调用时，函数中的 this 就是该对象；

   ```js
   var o={
   	a:10,
       b:{
       	a=12,
           fn:function(){
               console.log(this.a)
           }
       }
   }
   o.b.fn()
   //12
   ```

4. this永远指向最后调用它的对象

   ```js
   var o = {
   	a:10,
       b:{
       	a:12
           fn:function(){
       		console.log(this.a);
           	console.log(this);
       	}
       }
   }
   var j = o.b.fn;
   j();
   // undefined
   //window
   ```

5. 构造函数中的this指向

   ```js
   function Fn(){
   	this.user = '呵呵'
   }
   var p = new Fn()
   console.log(p.user) // 呵呵
   ```

6. 当this遇到return时

   ```js
   //如果返回值是一个对象，那么this指向的是构造函数的实例但是并没有被返回，如果返回值不是一个对象，那么this还是指向构造函数创建的实例。
   function fn(){
   	this.user = "呵呵"
       return {}
   }
   var a = new fn()
   console.log(a.user) //undefined
   ==============================
   function fn(){
   	this.user = "呵呵"
       return function(){}
   }
   var a = new fn()
   console.log(a.user) //undefined
   ==============================
   function fn(){
   	this.user = "呵呵"
       return 1
   }
   var a = new fn()
   console.log(a.user) //呵呵
   ==============================
   function fn(){
   	this.user = "呵呵"
       return undefined
   }
   var a = new fn()
   console.log(a.user) //呵呵
   ```

7. 箭头函数中的this指向

   ```js
   /*
   箭头函数是ES6中的特性，箭头函数没有执行上下文本所以没有this，它会沿用/捕获外部环境的this。也就是说，箭头函数内部与外部的this是保持一致的。
   */
   this.a = 20
   var test = {
   	a:40,
       init:()=>{
       	console.log(this.a)
           function go(){
           	this.a = 60
               console.log(this.a)
           }
           go.prototype.a = 50
           return go
       }
   }
   var p = test.init()
   p()
   new (test.init())()
   
   this.a = 20
   var test = {
   	a:40,
       init:function(){
       	console.log(this.a)
           function go(){
           	this.a = 60
               console.log(this.a)
           }
           go.prototype.a = 50
           return go
       }
   }
   var p = test.init()
   p()
   new (test.init())()
   ```

   

