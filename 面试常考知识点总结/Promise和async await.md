#### 题目一：

```js 
async function async1() {
  console.log('async1 start');
  await async2();
  console.log('async1 end');
}
async function async2() {
  console.log('async2 start');
  return new Promise((resolve, reject) => {
    resolve();
    console.log('async2 promise');
  })
} 
console.log('script start');
setTimeout(function() {
  console.log('setTimeout');
}, 0);
async1();
new Promise(function(resolve) {
  console.log('promise1');
  resolve();
}).then(function() {
  console.log('promise2');
}).then(function() {
  console.log('promise3');
});
console.log('script end')
```

答案：

```js
script start
async1 start
async2 start
async2 promise
promise1
script end
promise2
promise3
async1 end
setTimeout
```

解析：

```js
async function async1() {
    console.log('async1 start');//2
    await async2();
    //await后面的内容被阻塞，直到async2的回调执行完毕才会执行，也就是执行了6.5、7、8之后下面的async1 end才有推入微队列的时机
    console.log('async1 end'); //9
  }
  async function async2() {
    console.log('async2 start');//3
    return new Promise((resolve, reject) => {
      resolve();
      console.log('async2 promise');	//4 初始化promise时，传入的函数会立刻执行
    }).then(() => {console.log('假装有回调')})	//6 :5放入微任务
  } 
  console.log('script start');//1
  async1();
  new Promise(function(resolve) {
    console.log('promise1'); //5 初始化promise时，传入的函数会立刻执行
    resolve();
  }).then(function() {
    console.log('promise2');//7
  }).then(function() {
    console.log('promise3');//8
  });
  console.log('script end') //6
/*
如果async2()里不是返回的promise ，9那里应该就是首先进入微任务队列的 

但是 async2()里返回的是Promise，async2()返回的promise的then回调还没执行，所以不是resolved状态，即async2()还未全部执行完,还有一步停止上下文的操作，所以此时 async1 函数中 await后'async1 end‘的代码还没到推入微队列的时机。

只有当async2()中返回的promise的then回调执行完毕（也就是打印’假装有回调'）和微队列里面的其他微任务（打印promise2和promise3）全部清空后，async2()才算执行完毕，此时才会执行async1 end
*/
```



#### 题目二：

```js
async function async1() {
    console.log('async1 start');
    await async2();
    console.log('async1 end');
}
async function async2() {
    console.log('async2');
}
console.log('script start'); 
setTimeout(()=>{
    console.log('setTimeout');
},0)
async1();
new Promise((resolve)=>{
    console.log('promise1');
    resolve();
}).then(()=>{
    console.log('promise2');
});
console.log('script end');
```

答案：

```js
script start
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
```

#### 题目三：

```js
console.log(1);
setTimeout(() => {
 console.log(2);
 Promise.resolve().then(() => {
  console.log(3)
 });
});
new Promise((resolve, reject) => {
 console.log(4)
 resolve()
}).then(() => {
 console.log(5);
})
setTimeout(() => {
 console.log(6);
})
console.log(7);
```

答案：

```js
1
4
7
5
2
3
6
```

