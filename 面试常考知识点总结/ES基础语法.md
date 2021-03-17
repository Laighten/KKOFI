### 一、New一个新对象的过程

1. 先创建一个空对象；

   var obj = new Object();

2. 把函数的this指针指向这个空对象，并执行函数体；

   var result = Parent.call(obj);

3. 构建原型链，将新建对象的__ proto __赋值为Parent的prototype

   obj.__ proto __ = Parent.prototype

4. 判断返回值的类型，如果是值类型则直接返回obj，如果是引用类型则返回引用类型的对象；