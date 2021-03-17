#### 5、ES6中有哪些新加的语法？

**1、函数继承上的区别**

（1）ES5的继承

​		ES5的继承实质上是先创建子类的实例对象，然后再将父类的方法添加到this上（Parent.apply(this)），然后再把原型链继承。

**构造函数继承**：

```javascript
function Parent() {
    this.name = 'parent';
}
Parent.prototype.say = function () {
    console.log('say');
};
function Child(age) {
    Parent.call(this);
    this.age = age;
}
var p = new Parent(); //new一个Parent对象用来对比
p.say(); //输出say
var c = new Child(12);
c.age // 12
c.name //'parent'
c.say(); //undifined
/*
say是Parent原型链上的方法，Parent对象调用方法时，如果自身不存在就回去原型链上寻找，在原型链上找到了say方法，而Child对象没有继承Parent对象的原型链，所以它在向上寻找时就找不到，输出undifined。
*/
```

 **原型链继承：**

```javascript
function Parent() {
    this.name = 'parent';
}
Parent.prototype.say = function () {
    console.log('say');
};
function Child(age) {
    this.age = age;
}
Child.prototype = new Parent();
var c = new Child(12);
console.log(c.name); //输出parent
c.say() //输出say
/*
原型链继承是直接让Child构造函数的prototype直接指向Parent对象，这样Parent的东西Child对象可以直接从它的原型链上找到。缺点就是：当创建多个实例时，如果不同实例可能互相存在影响。
*/
```

**构造函数与原型链组合继承**：

```javascript
function Parent() {
    this.name = 'parent';
    this.arr = [1,2,3,4]
}
Parent.prototype.say = function () {
    console.log('say');
};
function Child(age) {
    Parent.call(this); 
    this.age = age;
}
Child.prototype = new Parent();
Child.prototype.constructor = Child;
var c1 = new Child(12);
var c2 = new Child(12);
console.log(c1.arr); //[1,2,3,4]
console.log(c2.arr);//[1,2,3,4]
c1.arr.push(5);
console.log(c1.arr); //[1,2,3,4,5]
console.log(c2.arr); //[1,2,3,4]
```

（2）ES6的继承

​		ES6的继承机制完全不同，实质上是先创建父类的实例对象this（所以必须先调用父类的super()方法，才可使用this关键字，否则报错。），然后再用子类的构造函数修改this实现继承。

```javascript
class Parent2 {
    constructor() {
        this.name = 'parent';
    }
}
Parent2.prototype.say = function () {
    console.log('say');
};
class Child2 extends Parent {
    constructor(age) {
        super();
        this.age = age;
    }
}
var c2 = new Child2(12);
console.log(c2.name); //输出parent
c2.say(); //输出say
console.log(c.constructor); 
//输出function Child(age) {Parent.call(this);this.age = age;}
console.log(new Parent().constructor); 
//输出Parent() {this.name = 'parent';this.arr = [1,2,3,4];}
```

（3）圣杯模式

​	圣杯模式的本质在于，中间生成了一个对象，起到了隔离的作用，今后为Son.prototype添加属性时，全部都会加在这个对象里面,所以不会对父级产生影响。而向上查找是沿着__proto__查找，可以顺利查找到父级的属性，实现继承。

```javascript
function Father(){}
function Son(){}   Father.prototype.lastName=‘Jack‘;
	//圣杯模式
function inherit(Target,Origin){
        function F(){};
        F.prototype = Origin.prototype;
        Target.prototype=new F();
}
inherit(Son,Father);
var son=new Son();
var father=new Father(); 
Son.prototype.sex=‘male‘;   
console.log(son.lastName);//Jack
console.log(son.sex);//male
console.log(father.sex);//undefined
```

**2、作用域的区别**

\* ES5中的作用域有---全局作用域、函数作用域

\* ES6中新增了---块级作用域（块级作用域由{}包裹，if语句、for语句中的{}也属于块级作用域）

**var：**

1. 没有块级作用域的概念
2. 有全局作用域、函数作用域的概念
3. 不初始化值默认为undefined
4. 存在变量提升
5. 全局作用域用var声明的变量会挂载到window对象下
6. 同一作用域中允许重复声明

**let：**

1. 有块级作用域的概念
2. 不存在变量提升
3. 暂时性死区

在块级作用域中，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。

1. 不存在全局作用域的概念
2. 同一块作用域中不允许重复声明

**const:** 

1. 与let特性一样，仅有2个差别
2. 区别1——必须立即初始化，不能留到以后赋值
3. 区别2——常量的值不能改变

参考：https://www.cnblogs.com/liu-di/p/11889683.html

**3、promise**

**4、箭头函数**

**5、数组中的展开运算符**

**6、set的属性方法**

​	Set对象是值的集合，Set 中的元素是唯一的，Set 对象允许你存储任何类型的唯一值，无论是原始值或者是对象引用。

![image-20210303165748813](./img/image-20210303165748813.png)