# JS原型相关

### 原型一把梭

![微信图片_20201203094255](C:\Users\Administrator\Desktop\JavaScriptLog\原型相关\微信图片_20201203094255.png)

`function Foo` 就是一个方法，比如JavaScript 中内置的 `Array`、`String` 等

`function Object` 就是一个 `Object`

`function Function` 就是 `Function`

以上都是 `function`，所以 `__proto__`都是`Function.prototype`

### 函数对象和普通对象

老话说，**万物皆对象**。而我们都知道在 JavaScript 中，创建对象有好几种方式，比如对象字面量，或者直接通过构造函数 `new` 一个对象出来：

![2](C:\Users\Administrator\Desktop\JavaScriptLog\原型相关\2.png)

**所谓的函数对象，其实就是 JavaScript 的用函数来模拟的类实现**。JavaScript 中的 Object 和 Function 就是典型的函数对象。

关于函数对象和普通对象，最直观的感受就是。。。咱直接看代码：

```
function fun1(){};
const fun2 = function(){};
const fun3 = new Function('name','console.log(name)');

const obj1 = {};
const obj2 = new Object();
const obj3 = new fun1();
const obj4 = new new Function();


console.log(typeof Object);//function
console.log(typeof Function);//function
console.log(typeof fun1);//function
console.log(typeof fun2);//function
console.log(typeof fun3);//function
console.log(typeof obj1);//object
console.log(typeof obj2);//object
console.log(typeof obj3);//object
console.log(typeof obj4);//object
```

上述代码中，`obj1`，`obj2`，`obj3`，`obj4`都是普通对象，`fun1`，`fun2`，`fun3` 都是 `Function` 的实例，也就是函数对象。

所以可以看出，**所有 Function 的实例都是函数对象，其他的均为普通对象，其中包括 Function 实例的实例**。

![3](C:\Users\Administrator\Desktop\JavaScriptLog\原型相关\3.png)

**JavaScript 中万物皆对象，而对象皆出自构造（构造函数）**

上图中，你疑惑的点是不是 `Function` 和 `new Function` 的关系。其实是这样子的：

```
Function.__proto__ === Function.prototype//true
```

### `__proto__`

首先我们需要明确两点：1️⃣`__proto__`和`constructor`是**对象**独有的。2️⃣`prototype`属性是**函数**独有的；

但是在 JavaScript 中，函数也是对象，所以函数也拥有`__proto__`和 `constructor`属性。

结合上面我们介绍的 `Object` 和 `Function` 的关系，看一下代码和关系图

```
 function Person(){…};
 let nealyang = new Person();
```

![4](C:\Users\Administrator\Desktop\JavaScriptLog\原型相关\4.png)

再梳理上图关系之前，我们再来讲解下`__proto__`。

![5](C:\Users\Administrator\Desktop\JavaScriptLog\原型相关\5.png)

`__proto__` 的例子，说起来比较复杂，可以说是一个历史问题。

ECMAScript 规范描述 `prototype` 是一个隐式引用，但之前的一些浏览器，已经私自实现了 `__proto__`这个属性，使得可以通过 `obj.__proto__` 这个显式的属性访问，访问到被定义为隐式属性的 `prototype`。

因此，情况是这样的，ECMAScript 规范说 `prototype` 应当是一个隐式引用:

- 通过 `Object.getPrototypeOf(obj)` 间接访问指定对象的 `prototype` 对象
- 通过 `Object.setPrototypeOf(obj, anotherObj)` 间接设置指定对象的 `prototype` 对象
- 部分浏览器提前开了 `__proto__` 的口子，使得可以通过 `obj.__proto__` 直接访问原型，通过 `obj.__proto__ = anotherObj` 直接设置原型
- ECMAScript 2015 规范只好向事实低头，将 `__proto__` 属性纳入了规范的一部分

从浏览器的打印结果我们可以看出，上图对象 `a` 存在一个`__proto__`属性。而事实上，他只是开发者工具方便开发者查看原型的故意渲染出来的一个虚拟节点。虽然我们可以查看，但实则并不存在该对象上。

`__proto__`属性既不能被 `for in` 遍历出来，也不能被 `Object.keys(obj)` 查找出来。

访问对象的 `obj.__proto__` 属性，默认走的是 `Object.prototype` 对象上 `__proto__` 属性的 get/set 方法。

### 原型链

这里我们需要知道的是，`__proto__`是对象所独有的，并且`__proto__`是**一个对象指向另一个对象**，也就是他的原型对象。我们也可以理解为父类对象。它的作用就是当你在访问一个对象属性的时候，如果该对象内部不存在这个属性，那么就回去它的`__proto__`属性所指向的对象（父类对象）上查找，如果父类对象依旧不存在这个属性，那么就回去其父类的`__proto__`属性所指向的父类的父类上去查找。以此类推，知道找到 `null`。而这个查找的过程，也就构成了我们常说的**原型链**。

### prototype

在规范里，prototype 被定义为：给其它对象提供共享属性的对象。`prototype` 自己也是对象，只是被用以承担某个职能罢了.

所有对象，都可以作为另一个对象的 `prototype` 来用。

![6](C:\Users\Administrator\Desktop\JavaScriptLog\原型相关\6.jpg)

修改`__proto__`的关系图，我们添加了 `prototype`,**prototype是函数所独有的**。**它的作用就是包含可以给特定类型的所有实例提供共享的属性和方法。它的含义就是函数的远行对象，**也就是这个函数所创建的实例的远行对象，正如上图：`nealyang.__proto__ === Person.prototype`。任何函数在创建的时候，都会默认给该函数添加 `prototype` 属性.

### constructor

**constructor属性也是对象所独有的**，它是**一个对象指向一个函数，这个函数就是该对象的构造函数**。

注意，每一个对象都有其对应的构造函数，本身或者继承而来。单从`constructor`这个属性来讲，只有`prototype`对象才有。每个函数在创建的时候，JavaScript 会同时创建一个该函数对应的`prototype`对象，而`函数创建的对象.__proto__ === 该函数.prototype`，该`函数.prototype.constructor===该函数本身`，故通过函数创建的对象即使自己没有`constructor`属性，它也能通过`__proto__`找到对应的`constructor`，所以任何对象最终都可以找到其对应的构造函数。

唯一特殊的可能就是我开篇抛出来的一个问题。JavaScript 原型的老祖宗：`Function`。它是它自己的构造函数。所以`Function.prototype === Function.__proto`。

为了直观了解，我们在上面的图中，继续添加上`constructor`：

![7](C:\Users\Administrator\Desktop\JavaScriptLog\原型相关\7.jpg)

其中 `constructor` 属性，**虚线表示继承而来的 constructor 属性**。

`__proto__`介绍的原型链，我们在图中直观的标出来的话就是如下这个样子

![8](C:\Users\Administrator\Desktop\JavaScriptLog\原型相关\8.jpg)

## typeof && instanceof 原理

### typeof

#### 基本用法

`typeof` 的用法相比大家都比较熟悉，一般被用于来判断一个变量的类型。我们可以使用 `typeof` 来判断`number`、`undefined`、`symbol`、`string`、`function`、`boolean`、`object` 这七种数据类型。但是遗憾的是，`typeof` 在判断 `object` 类型时候，有些许的尴尬。它并不能明确的告诉你，该 `object` 属于哪一种 `object`。

```
let s = new String('abc');
typeof s === 'object'// true
typeof null;//"object"
```

### instanceof

`instanceof` 和 `typeof` 非常的类似。`instanceof` 运算符用来检测 `constructor.prototype`是否存在于参数 `object` 的原型链上。与 `typeof` 方法不同的是，`instanceof` 方法要求开发者明确地确认对象为某特定类型。

#### 基本用法

```
// 定义构造函数
function C(){}
function D(){}

var o = new C();


o instanceof C; // true，因为 Object.getPrototypeOf(o) === C.prototype


o instanceof D; // false，因为 D.prototype 不在 o 的原型链上

o instanceof Object; // true，因为 Object.prototype.isPrototypeOf(o) 返回 true
C.prototype instanceof Object // true，同上

C.prototype = {};
var o2 = new C();

o2 instanceof C; // true

o instanceof C; // false，C.prototype 指向了一个空对象,这个空对象不在 o 的原型链上.

D.prototype = new C(); // 继承
var o3 = new D();
o3 instanceof D; // true
o3 instanceof C; // true 因为 C.prototype 现在在 o3 的原型链上
```

## ES5 中的继承实现方式

原型继承分为两大类，显式继承和隐式继承。类式继承  构造函数继承 组合式继承 原型式继承 寄生式继承 寄生组合式继承

### new 关键字

在讲解继承之前呢，我觉得 `new` 这个东西很有必要介绍下~

一个例子看下`new` 关键字都干了啥

```
function Person(name,age){
  this.name = name;
  this.age = age;
  
  this.sex = 'male';
}

Person.prototype.isHandsome = true;

Person.prototype.sayName = function(){
  console.log(`Hello , my name is ${this.name}`);
}

let handsomeBoy = new Person('Nealyang',25);

console.log(handsomeBoy.name) // Nealyang
console.log(handsomeBoy.sex) // male
console.log(handsomeBoy.isHandsome) // true

handsomeBoy.sayName(); // Hello , my name is Nealyang
```

从上面的例子我们可以看到：

- 访问到 `Person` 构造函数里的属性

- 访问到 `Person.prototype` 中的属性

  

