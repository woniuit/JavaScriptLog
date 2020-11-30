## 同步

每条指令都会严格按照它们出现的顺序来执行。如果代码要访问一些高延迟的资源，比如向远程服务器发送请求并等待响应，那么就会出现长时间的等待。

## 异步

Promises
let p = new Promise(() => {})

##### Promises状态

1.待定（pending）
promises的最初状态。pending状态可以转换为resolved状态或者rejected状态。只要从待
定转换为解决或拒绝，promises的状态就不再改变。
promises的状态是私有的，不能直接通过 JavaScript 检测到。这主要是为了避免根据读取到
的promises状态，以同步方式处理promises对象。另外，promises的状态也不能被外部 JavaScript 代码修改。这与不
能读取该状态的原因是一样的：promises故意将异步行为封装起来，从而隔离外部的同步代码。
2.解决 (resolved）

3.拒绝（rejected）

## promise的缺点

首先，无法取消`Promise`，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部。第三，当处于`pending`状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

## promise的基本用法

es6规定promise为一个构造函数,promise接收一个函数作为参数。该函数的两个参数分别是`resolve`和`reject`，它们又分别是两个函数。resolve把promise对象从未完成到成功。reject是把promise对象从未完成到失败

```
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```



## promise中的then

该方法可以处理promise构造函数中的变化，有两个参数，第一个函数接收resolved状态的执行，第二个参数接收reject状态的执行。其中第二个参数是可选的。then方法的执行结果也会返回一个Promise对象。因此我们可以进行then的链式执行，这也是解决回调地狱的主要方式

```javascript
function fn(num) {
    return new Promise(function(resolve, reject) {
        if (typeof num == 'number') {
            resolve();
        } else {
            reject();
        }
    }).then(function() {
        console.log('参数是一个number值');
    }, function() {
        console.log('参数不是一个number值');
    })
}

fn('hahha');
fn(1234);
```

#### Promise 新建后就会立即执行。

```
let promise = new Promise(function(resolve, reject) {
  console.log('Promise');
  resolve();
});

promise.then(function() {
  console.log('resolved.');
});

console.log('Hi!');

// Promise
// Hi!
// resolved

//上面代码中，Promise 新建后立即执行，所以首先输出的是Promise。然后，then方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行，所以resolved最后输出。
```



## Promise.all

**Promise.all可以将多个Promise实例包装成一个新的Promise实例。同时，成功和失败的返回值是不同的，成功的时候返回的是一个结果数组，而失败的时候则返回最先被reject失败状态的值。**

需要特别注意的是，Promise.all获得的成功结果的数组里面的数据顺序和Promise.all接收到的数组顺序是一致的，即p1的结果在前，即便p1的结果获取的比p2要晚。这带来了一个绝大的好处：在前端开发请求数据的过程中，偶尔会遇到发送多个请求并根据请求顺序获取和使用数据的场景，使用Promise.all毫无疑问可以解决这个问题。

## Promise.race

Promse.race就是赛跑的意思，意思就是说，Promise.race([p1, p2, p3])里面哪个结果获得的快，就返回那个结果，不管结果本身是成功状态还是失败状态。