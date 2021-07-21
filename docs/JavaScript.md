## 数据类型

### 1. JS 数据类型有哪些？引用数据类型有哪些？

JS 数据类型分为两大类，九个数据类型。

1. 原始对象（原始值）
2. 对象类型（引用数据类型）

其中原始类型又分为七种类型，分别为：

- boolean
- number
- string
- undefined
- null
- symbol
- **bigint**

对象类型分为两种，分别为：

- Object
- Function

引用数据类型：**对象 Object**（包含普通对象-**Object**， 数组对象-**Array**，正则对象-**RegExp**，数学函数-**Math**，函数对象-**Function**，es6 的-**Map**、**Set**）等。

原始类型存储在栈上，对象类型存储在堆上，但是它的引用地址还是存在栈上。

### 2. 说出下面运行的结果，解释原因。

```js
function test(person) {
  person.age = 26;
  person = {
    name: 'lme',
    age: 25,
  };
  return person;
}
const p1 = {
  name: 'hlw',
  age: 19,
};
const p2 = test(p1);
console.log(p1); // -> ?
console.log(p2); // -> ?
```

结果：

```js
p1: {name: 'hlw', age: 26}
p2: {name: 'lme', age: 25}
```

> 原因：在函数传参的时候传递的是对象在堆中的内存地址值，test 函数中的实参 person 是 p1 对象的内存地址，通过调用 person.age = 26 确实改变了 p1 的值，但随后 person 变成了另一块存储空间的地址，并且在最后将这另外一份内存空间的地址返回，赋给了 p2。

### 3. null 是对象吗？为什么？

结论：null 不是对象

解释：虽然 typeof null 会输出 object，但是这只是 JS 存在的一个悠久 Bug。在 JS 的最终版本中使用的是 32 位系统，为了性能考虑使用低位存储变量的类型信息，000 开头代表是对象然而 null 表示为全 0，所以将它错误的判断为 object，具体判断 null 类型的话直接 xxx === null 即可。

### 4. '1'.toString()为什么可以调用？

其实在这个语句运行的过程中做了这样几件事情：

```js
var s = new Object('1');
s.toString();
s = null;
```

第一步：创建 Object 类实例。注意为什么不是 String？由于 Symbol 和 Bigint 的出现，对它们调用 new 都会报错，目前 ES6 规范也不建议用 new 来创建基本类型的包装类。

第二步：调用实例方法。

第三步：执行完方法立即销毁这个实例。

整个过程体现了**基本包装类型**的性质，而基本包装类型恰恰属于数据类型，包括 Boolean，Number 和 String。

### 5. 0.1+0.2 为什么不等于 0.3

0.1 和 0.2 在转换成二进制后会无限循环，由于标准位数的限制后面多余的位数会被截掉，此时就已经出现了精度的损失，相加后因浮点数小数位的限制而截断的二进制数字在转换为十进制就会变成 0.30000000000000004

### 6. typeof 是否能正确判断类型？

对于原始类型来说，除了 null 都可以调用 typeof 显示正确的类型。

```js
typeof 1; // 'number'
typeof '1'; // 'string'
typeof undefined; // 'undefined'
typeof true; // 'boolean'
typeof Symbol(); // 'symbol'
typeof 1n; // 'bigint'
```

对于对象类型来说，typeof 只能具体判断函数的类型为 function，其它均为 object

```js
typeof []; // 'object'
typeof {}; // 'object'
typeof console.log; // 'function'
```

因此采用 typeof 判断对象数据类型是不合适的，采用 instanceof 会更好，instanceof 的原理是基于原型链的查询，只要处于原型链中，判断永远 true

```js
const Person = function() {};
const p1 = new Person();
p1 instanceof Person; // true

var str1 = 'hello word';
str1 instanceof String; // false

var str2 = new String('hello word');
str2 instanceof String; // true
```

### 7. instanceof 能否判断基本数据类型？ 还有哪些可以判断数据类型

instanceof 内部通过原型链的方式来判断是否为构建函数的实例，常用于判断具体的对象类型。

能，比如可以通过 hake 的方式得以实现

```js
class checkIsNumber {
  static [Symbol.hasInstance](x) {
    return typeof x === 'number';
  }
}
console.log(111 instanceof checkIsNumber); // true
```

其实就是自定义 instanceof 行为的一种方式，这里将原有的 instanceof 方法重定义，换成了 typeof，因此能够判断基本数据类型。

另外其实我们还可以直接通过构建函数来判断类型：

```js
// true
[].constructor === Array;
```

还有一种终极版本：Object.prototype.toString

前几种方式或多或少都存在一些缺陷，Object.prototype.toString 综合来看是最佳选择，能判断的类型最完整。

```js
Object.prototype.toString.call(null); // '[object Null]'
Object.prototype.toString.call(1); // '[object Number]'
Object.prototype.toString.call(''); // '[object String]'
Object.prototype.toString.call(1n); // '[object BigInt]'
Object.prototype.toString.call([]); // '[object Array]'
Object.prototype.toString.call({}); // '[object Object]'
Object.prototype.toString.call(function() {}); // '[object Function]'
```

[object XXX] 中的 XXX 就是判断出来的类型。

isXXX API
同时还存在一些判断类型的 API，写两个常见的

```js
Array.isArray([]); // true
isNaN(','); // true
```

### 8. 手动实现一下 instanceof 的功能

核心：原型链向上查找

```js
function myInstanceof(value, dataType) {
  // 基本数据类型直接返回false
  if (typeof value !== 'object' || value === null) return false;
  // getPrototypeOf是Object对象自带的一个方法，能够拿到参数的原型对象
  let proto = Object.getPrototypeOf(value);
  while (true) {
    // 查找到尽头，还没找到
    if (proto == null) return false;
    // 找到相同的原型对象
    if (proto == dataType.prototype) return true;
    proto = Object.getPrototypeOf(proto);
  }
}
```

测试如下：

```js
console.log(myInstanceof('111', String)); //false
console.log(myInstanceof(new String('111'), String)); //true
```

### 9.Object.is 和===的区别？

Object 在严格等于的基础上修复了一些特殊情况下的失误，具体来说就是+0 和-0，NaN 和 NaN。

源码如下：

```js
function is(x, y) {
  if (x === y) {
    // 运行到1/x === 1/y的时候x和y都为0，但是1/+0 = +Infinity，1/-0 = -Infinity，是不一样的
    return x !== 0 || y !== 0 || 1 / x === 1 / y;
  } else {
    // NaN === NaN是false，这是不对的，我们在这里做一个拦截，x !== x，那么一定是NaN，y 同理
    // 两个都是NaN的时候返回true
    return x !== x && y !== y;
  }
}
```

### 10. [] == ![] 结果是什么？为什么？

解析：

== 中，左右两边都需要转换为数字然后进行比较。

[]转换为数字为 0。

![] 首先是转换为布尔值，由于[]作为一个引用类型转换为布尔值为 true。

因此![]为 false，进而再转换数字变为 0。

0 == 0，结果为 true

### 11. 类型转换分为几种？

类型转换分为两种情况，分别为强制转换及隐式转换。

#### 11.1 强制转换

强制转换就是转成特定的类型：

```js
Number(false); // -> 0
Number('1'); // -> 1
Number('sb'); // -> NaN
(1).toString(); // -> '1'
```

主要是强转数字和布尔值的规则。

##### 11.1.1 转布尔值规则

- **undefined**、**nulll**、**false**、**NaN**、 **''** 、**0**、**-0**都转为**fasle**
- 其他所有值都转为**true**，包括所有对象

##### 11.1.2 转数字规则

- true 为 1，false 为 0
- null 为 0，undefined 为 NaN，symbol 报错
- 字符串看内容，如果是数字或者进制值就正常转，否则就 NaN
- 对象的规则隐式转换再转

#### 11.2 隐式转换

##### 11.2.1 对象转基本类型

- 如果 Symbol.toPrimitive()方法，优先调用再返回
- 调用 valueOf()，如果转换为原始类型，则返回
- 调用 toString()，如果转换为原始类型，则返回
- 如果都没有返回原始类型，会报错

##### 11.2.2 四则运算符

- 只有当加法运算时，其中一方是字符串类型，就会把另一个也转为字符串类型
- 其他运算只要一方是数字，那么另一方就转为数字

转换具体规则如下:

|        原始值         | 转换目标 |                         结果                         |
| :-------------------: | :------: | :--------------------------------------------------: |
|        number         |  布尔值  |              除了 0，-0，NaN 都为 true               |
|        string         |  布尔值  |                  除了空串都为 true                   |
|    undefined、null    |  布尔值  |                        False                         |
|       引用类型        |  布尔值  |                         True                         |
|        number         |  字符串  |                       5 => '5'                       |
| Boolean、函数、Symbol |  字符串  |                        'true'                        |
|         数组          |  字符串  |                    [1,2] => '1,2'                    |
|         对象          |  字符串  |                  '[object Object]'                   |
|        string         |   数字   |                 '1' => 1，'a' => NaN                 |
|         数组          |   数字   | 空数组为 0，存在一个元素且为数字转数字，其他清空 NaN |
|         null          |   数字   |                          0                           |
|  除了数组的引用类型   |   数字   |                         NaN                          |
|        Symbol         |   数字   |                         抛错                         |

### 12. == 和 === 有什么区别

===叫做严格相等、是指：左右两边不仅值要相等，类型也要相等，例如：'1' === 1 的结果是 false，是因为一边是 string，另一边是 number

== 不像=== 那样严格，对于一般情况，只要值相等，就返回 true，但==还设计一些类型转换，它的转换规则如下：

- 两边的类型是否相同，相同的话就比较值的大小，例如 1==2，返回 false
- 判断的是否是 null 和 undefined，是的话就返回 true
- 判断的类型是否是 String 和 Number，是的话，把 String 类型转换成 Number，再进行比较
- 判断一方是否是 Boolean，是的话就把 Boolean 转换成 Number，再进行比较
- 如果其中一方为 Object，且另一方为 String、Number 或者 Symbol，会将 Object 转换成字符串，再进行比较

```js
console.log({ a: 1 } == true); // -> false
console.log({ a: 1 } == '[object Object]'); // -> true
```

### 13. 对象转原始类型是根据什么流程运行的？

11.2.1 写了 重新再写一遍(单独重新缕下)

对象转原始类型，会调用内置的[ToPrimitive]函数，对于该函数而言，其逻辑如下：

1. 如果 Symbol.toPrimitive()方法，优先调用再返回
2. 调用 valueOf()，如果转换为原始类型，则返回
3. 调用 toString()，如果转换为原始类型，则返回
4. 如果都没有返回原始类型，会报错

```js
var obj = {
  value: 3,
  valueOf() {
    return 4;
  },
  toString() {
    return '5';
  },
  [Symbol.toPrimitive]() {
    return 6;
  },
};
console.log(obj + 1); // -> 7
```

### 14.如何让 if(a==1 && a==2)条件成立？

说白了，其实就是 13 题的应用 看代码：

```js
var a = {
  value: 0,
  valueOf() {
    this.value++;
    return this.value;
  },
};
console.log(a == 1 && a == 2); // -> true
```

### 15. 谈谈你对闭包的理解

什么是闭包？

红宝石(p178)上对闭包的定义：闭包是指有权访问另外一个函数作用域中的变量的函数

MDN 对闭包的定义为：闭包是指那些能够访问自由变量的函数

(其中自由变量，指在函数中使用的，但既不是函数参数 arguments 也不是函数的局部变量的变量，其实就是另外一个函数作用域中的变量)

### 16. 闭包产生的原因？

首先要明白作用域的概念，其实很简单，在 ES5 中只存在两种作用域——**全局作用域**和**函数作用域**，`当访问一个变量时，解释器会首先在当前作用域查找标示符，如果没找到，就去父作用域查找，直到找到该变量的标示符或者不在父作用域中，这就是作用域`，值得注意的是，每个子函数都会拷贝上级的作用域，形成一个作用域的链条，比如：

```js
var a = 1;
function f1() {
  var a = 2;
  function f2() {
    var a = 3;
    console.log(a); // -> 3
  }
}
```

在这段代码中，f1 的作用域指向全局(window)和它本身，而 f2 的作用域指向全局作用域(window)、f1 和它本身。而且作用域是从最底层向上找，直到找到全局作用域 window 为止，如果全局还没有的话就会报错。emmm... 就是这么简单

闭包产生的本质就是：当前环境中存在指向父级作用域的引用。

```js
function f1() {
  var a = 2;
  function f2() {
    console.log(a); // -> 2
  }
  return f2;
}
var x = f1();
x();
```

这里 x 会拿到父级作用域中的变量输出 2，因为在当前环境中，含有对 f2 的引用，f2 恰恰引用了 window、f1 和 f2 的作用域，因此 f2 可以访问到 f1 的作用域的变量。

那是不是只有返回函数才算是产生了闭包呢？

回到闭包的本质：我们只需要让父级作用域的引用存在即可，那么可以这么做，如下 ：

```js
var f3;
function f1() {
  var a = 2;
  f3 = function() {
    console.log(a);
  };
}
f1();
f3();
```

让 f1 先执行，给 f3 赋值后，等于说现在 f3 拥有了`window、f1和f3本身这几个作用域的访问权限`，还是自底向上查找，最近是在 f1 中找到了 a，所以输出 2

在这里是外面的变量 f3 存在父级作用域的引用，因此产生了闭包，形式变了，本质没有变。

#### 16.1 闭包有哪些表达形式？

明白了本质以后，我们就来看看，在真实的场景中，究竟在那些地方能体现闭包的存在？

1. 返回一个函数。
2. 作为函数参数传递

```js
var a = 1;
function foo() {
  var a = 2;
  function baz() {
    console.log(a);
  }
  bar(baz);
}
function bar(fn) {
  // 这就是闭包
  fn();
}
foo(); // 输出2，而不是1
```

3. 在定时器、时间监听、Ajax 请求、跨窗口通信、Web Workers 或者任何异步中，只要使用了回调函数，实际上就是在使用闭包

以下的闭包保存的仅仅是 window 和当前作用域。

```js
// 定时器
setTimeout(function timeHandler() {
  console.log('123');
}, 100);
// 事件监听
$('#app').click(function() {
  console.log('DOM Listener');
});
```

4. IIFE(立即执行函数表达式)创建闭包，保存了`全局作用域window`和`当前函数的作用域`，因此可以全局的变量。

```js
var a = 2;
(function IIFE() {
  console.log(a);
})();
```

#### 16.2 如何解决下面的循环输出问题？

```js
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, i * 1000);
}
```

为什么会全部输出 6？如何改进，让它输出 1，2，3，4，5？

因为 setTimeout 为宏任务，由于 JS 中单线程 eventLoop 机制，在主线程同步任务执行完成后才去执行宏任务，因此循环结束后 setTimeout 中的回调才依次执行，但输出 i 的时候当前作用域没有，往上一级在找，发现了 i，但是此时循环已经结束，i 变成了 6，所以会全部输出 6

解决方法

1. 使用闭包的方式

```js
for (var i = 1; i <= 5; i++) {
  (function(j) {
    setTimeout(function timer() {
      console.log(j);
    }, j * 1000);
  })(i);
}
```

我们首先使用了立即执行函数将 i 传入函数内部，这个时候值就被固定在了参数 j 上面不会改变，当下次执行 timer 这个闭包的时候，就可以使用外部函数的变量 j，从而达到目的。

2. 使用 setTimeout 的第三个参数，这个参数会被当成 timer 函数的参数传入。

```js
for (var i = 1; i <= 5; i++) {
  setTimeout(
    function timer(j) {
      console.log(j);
    },
    i * 1000,
    i
  );
}
```

3. 使用 let 定义 i 即可

```js
for (let i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, i * 1000);
}
```

### 17. 谈谈你对 JS 中 this 的理解，箭头函数的 this 是什么？

其实 JS 中 this 是一个非常简单的东西，只要理解它的实行顺序就 ok 了。

隐式绑定的场景讨论

1. 全局上下文
2. 直接调用函数
3. 对象.方法的形式调用
4. DOM 事件绑定（特殊）
5. new 构造函数绑定
6. 箭头函数

#### 1. 全局上下文

全局上下文默认 this 指向 window，严格模式下指向 undefined

#### 2. 直接调用函数

```js
let obj = {
  a: function() {
    console.log(this);
  },
};
let func = obj.a;
func();
```

这种情况是直接调用。this 相当于全局上下文的情况。

#### 3. 对象.方法的形式调用

还是刚刚那个 demo，我如果这样写：

```js
obj.a();
```

这就是**对象.方法**的情况，this 指向这个对象

#### 4. DOM 事件绑定

onclick 和 addEventlistener 中 this 默认指向绑定事件的元素。

IE 比较奇怪，使用 attachEvent，里面的 this 默认指向 window

#### 5. new+ 构造函数

此时构造函数中的 this 指向实例对象

#### 6. 箭头函数？？？

箭头函数没有 this，因此也不能绑定，里面的 this 会指向当前的非箭头函数的 this，找不到就是 window（严格模式 undefined），通俗点就是箭头函数中的 this 只取决包裹箭头函数的第一个普通函数的 this。

比如：

```js
let obj = {
  a: function() {
    let oye = () => {
      console.log(this);
    };
    oye();
  },
};
obj.a(); // 找到最近的非箭头函数a，a现在绑定着obj, 因此箭头函数中的this是obj
```

显式绑定

call/apply/bind 可以显式绑定

最后种情况也就是 bind 这些改变上下文的 API 了，对于这些函数来说，this 取决于第一个参数，如果第一个参数为空，那么就是 window。

```js
let a = {};
let fn = function() {
  console.log(this);
};
fn.bind().bind(a)(); // => ?
```

如果你认为输出结果是 a，那么你就错了，其实我们可以把上述代码转换成另一种形式

```js
// fn.bind().bind(a) 等于
let fn2 = function fn1() {
  return function() {
    return fn.apply();
  }.apply(a);
};
fn2();
```

可以从上述代码中发现，不管我们给函数 bind 几次，fn 中的 this 永远由第一次 bind 决定，所以结果永远是 window。

```js
let a = { name: 'lme' };
function foo() {
  console.log(this.name);
}
foo.bind(a)(); // => 'lme'
```

> 优先级: new > call、apply、bind > 对象.方法 > 直接调用。

### 18. JS 中浅拷贝的手段有哪些？

#### 重要：什么是拷贝？

首先来直观的感受一下什么是拷贝？

```js
let arr = [1, 2, 3];
let newArr = arr;
newArr[0] = 100;
console.log(arr); // -> [100, 2, 3]
```

这里是直接赋值的情况，不设计任何拷贝，当改变 newArr 的时候，由于是同一个引用，arr 指向的值也跟着改变

现在进行浅拷贝

```js
let arr = [1, 2, 3];
let newArr = arr.slice();
newArr[0] = 100;
console.log(arr); // -> [1, 2, 3]
```

当修改 newArr 的时候，arr 的值并不改变，因为 newArr 是 arr 浅拷贝后的结果，newArr 和 arr 现在引用的已经不是同一块空间。

但是又带来一个潜在的问题：

```js
let arr = [1, 2, { val: 4 }];
let newArr = arr.slice();
newArr[2].val = 1000;

console.log(arr); // -> [ 1, 2, { val: 1000 } ]
```

看，我* 你*个 \*，浅拷贝只能拷贝一层对象，如果有对象的嵌套，那么浅拷贝将无能为力，但深拷贝就是为了解决这个问题而生的，它能解决无限级的对象嵌套问题，实现彻底的拷贝。

#### 1. 手动实现

```js
const deepClone = (target) => {
  if (typeof target === 'object' && target !== null) {
    const cloneTarget = Array.isArray(target) ? [] : {};
    for (let prop in target) {
      if (target.hasOwnProperty(prop)) {
        cloneTarget[prop] = deepClone(target[prop]);
      }
    }
    return cloneTarget;
  } else {
    return target;
  }
};
let obj = {
  a: [1, 2, 3],
  b: {
    c: 2,
    d: 3,
  },
};
let newObj = deepClone(obj);
newObj.b.c = 1;
console.log(obj.b.c); // -> 2
console.log(newObj.b.c); // -> 1
```

#### 1.1 手动实现第二种

```js
function deepClone(obj) {
  function isObject(o) {
    return (typeof o === 'object' || typeof o === 'function') && o !== null;
  }

  if (!isObject(obj)) {
    throw new Error('非对象');
  }

  let isArray = Array.isArray(obj);
  let newObj = isArray ? [...obj] : { ...obj };
  Reflect.ownKeys(newObj).forEach((key) => {
    newObj[key] = isObject(obj[key]) ? deepClone(obj[key]) : obj[key];
  });

  return newObj;
}
```
