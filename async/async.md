# async

##### 概念：**一句话，async 函数就是 Generator 函数的语法糖。**

例子：

```
function fn() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('success')
        })
    })
}

async test() {
    let result = await fn() //因为fn会返回一个Promise对象
    console.log(result)    //这里会打出Promise成功后传递过来的'success'
}

test()
```

## async/await的错误处理方式

```
let promiseDemo = new Promise((resolve, reject) => {
    setTimeout(() => {
        let random = Math.random()
        if (random >= 0.5) {
            resolve('success')
        } else {
            reject('failed')
        }   
    }, 1000)
})

async function test() {
    let result = await promiseDemo
    return result  //这里的result是promiseDemo成功状态的值，如果失败了，代码就直接跳到下面的catch了
}

test().then(response => {
    console.log(response) 
}).catch(error => {
    console.log(error)
})
```

上面的代码需要注意两个地方，一是async函数需要主动return一下，如果Promise的状态是成功的，那么return的这个值就会被下面的then方法捕捉到；二是，如果async函数有任何错误，都被catch捕捉到！



## 同步与异步

在async函数中使用await，那么await这里的代码就会变成同步的了，意思就是说只有等await后面的Promise执行完成得到结果才会继续下去，await就是等待，这样虽然避免了异步，但是它也会阻塞代码，所以使用的时候要考虑周全。

```
function fn(name) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(`${name}成功`)
        }, 1000)
    })
}

async function test() {
    let p1 = await fn('小红')
    let p2 = await fn('小明')
    let p3 = await fn('小华')
    return [p1, p2, p3]
}

test().then(result => {
    console.log(result)
}).catch(result => {
    console.log(result)
})

```

这样写虽然是可以的，但是这里await会阻塞代码，每个await都必须等后面的fn()执行完成才会执行下一行代码，所以test函数执行需要3秒。如果不是遇到特定的场景，最好还是不要这样用。



```
console.log(1)
let promiseDemo = new Promise((resolve, reject) => {
    console.log(2)
    setTimeout(() => {
        let random = Math.random()
        if (random >= 0.2) {
            resolve('success')
            console.log(3)
        } else {
            reject('failed')
            console.log(3)
        }   
    }, 1000)
})

async function test() {
    console.log(4)
    let result = await promiseDemo
    return result
}

test().then(result => {
    console.log(5)
}).catch((result) => {
    console.log(5)
})

console.log(6)

答案是：1 2 4 6 3 5
```

### 适合使用async/await的业务场景

在前端编程中，我们偶尔会遇到这样一个场景：我们需要发送多个请求，而**后面请求的发送总是需要依赖上一个请求返回的数据**。对于这个问题，我们既可以用的Promise的链式调用来解决，也可以用async/await来解决，然而后者会更简洁些。

使用Promise链式调用来处理：

```
function request(time) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(time)
        }, time)
    })
}

request(500).then(result => {
    return request(result + 1000)
}).then(result => {
    return request(result + 1000)
}).then(result => {
    console.log(result)
}).catch(error => {
    console.log(error)
}) 

```

使用async/await的来处理：

```
function request(time) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(time)
        }, time)
    })
}

async function getResult() {
    let p1 = await request(500)
    let p2 = await request(p1 + 1000)
    let p3 = await request(p2 + 1000)
    return p3
}

getResult().then(result => {
    console.log(result)
}).catch(error => {
    console.log(error)
})

```

