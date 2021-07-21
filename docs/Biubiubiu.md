## CSS

###CSS 选择器

* 元素选择器
* 类选择器
* ID 选择器
* *号通用选择器
* 后代选择器
* 相邻兄弟选择器
* 子选择器
* 伪类选择器

###CSS 盒模型

####盒模型的组成，普通盒模型和怪异盒模型的区别

盒模型的组成： content（内容区） + padding（填充区）+ border（边框区） + margin（外界区）

* 标准盒模型：在标准下，一个块的总宽度 = width + margin + padding + border
* 怪异盒模型： IE盒模型或者怪异盒模型显而易见的区别就是width 和 height 除了content区域外，还包含padding和border，一个块的总宽度 = width + margin （width已经包含了padding 和 border的值）

#### 切换盒模型

* 标准盒模型：box-sizing：content-box
* IE 盒模型：box-sizing：border-box

### BFC

BFC 即 块级格式化上下文 

**具有BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会再布局上影响到外面的元素，并且 BFC 具有 普通容器 所没有的一些特性**

#### 触发BFC

只要元素满足下面任一条件 即可触发BFC

* body 根元素
* 浮动元素：float 除 none 以外的值
* 绝对定位元素：position （absoloute、fixed）
* display 为 inline-block、table-cells、flex 
* overflow 除了 visible 以外的值（hidden、auto、scroll）

解决：高度塌陷

### 权重记忆口诀

从0开始，一个行内样式+1000，一个id选择器+100，一个属性选择器、class或伪类+10，一个元素选择器，或者伪元素+1，通配符+0

### FIex 布局

## 基础必问

### reconcile算法（比较算法）            

#### 	什么是虚拟Dom

虚拟DOM指的就是将真实的DOM树构造成js对象的形式，从而解决浏览器操作真是DOM的性能问题 

Diff的用途就是在新老虚拟DOM之间找到最小更新的部分，从而将该部分对应的DOM进行更新。

####		Diff的流程

1. 首先比较新旧节点是不是同一个节点（可以通过 sel 选择器 和 key （唯一标识）值是不是相同），不是同一个节点就进行暴力删除（注： 先以旧节点为基准插入新节点，然后再删除旧节点）
2. 若是同一个节点则需要进一步的比较
   	- 完全相同，不做处理
      	- 新节点内容为文本，直接替换完事
      	- 新节点有子节点，若老节点没有子元素，则直接清空老节点，将新节点的子元素插入即可，老节点有子元素则就需要上述更新策略

###Vue 和 React diff 算法的区别

Vue 和 React 的 diff 都是忽略跨级比较，只做同级比较。

* vue对比节点，当节点元素类型相同，但是className不同，认为是不同的类型元素，删除重建，而React 会认为是同类型节点，只是修改节点属性
* Vue 的列表对比，采用从两端到中间的对比方式，而 React 则采用从左到右依次比对的方式。当一个集合，只是把最后一个节点移动到了第一个， React 会把前面的节点一次移动， 而 Vue 只会把最后一个节点移动到第一个。总体上， Vue 的对比方式更高效。

### new 的过程

当我们 new 一个构造函数的时候，得到的实例继承了构造器的构造属性以及原型上的属性

当我们 new 一个构造器，主要有是三步：

	* 构建一个空对象，将它的引用赋给this，继承函数的原型
	* 通过 this 将属性和方法添加至这个对象
	* 最后返回 this 指向的新对象，也就是实例（如果没有手动返回其他的对象）

~~~javascript
// 构造器函数
let Parent = function (name, age) {
    this.name = name;
    this.age = age;
};
Parent.prototype.sayName = function () {
    console.log(this.name);
};
//自己定义的new方法
let newMethod = function (Parent, ...rest) {
    // 1.以构造器的prototype属性为原型，创建新对象；
    let child = Object.create(Parent.prototype);
    // 2.将this和调用参数传给构造器执行
    let result = Parent.apply(child, rest);
    // 3.如果构造器没有手动返回对象，则返回第一步的对象
    return typeof result  === 'object' ? result : child;
};
//创建实例，将构造函数Parent与形参作为参数传入
const child = newMethod(Parent, 'echo', 26);
child.sayName() //'echo';

//最后检验，与使用new的效果相同
child instanceof Parent//true
child.hasOwnProperty('name')//true
child.hasOwnProperty('age')//true
child.hasOwnProperty('sayName')//false
~~~

### let const var 区别

* var 声明变量具备变量提升，let const 不具备变量提升，先定义后使用， 不然会报错
* let const 都具有块级作用域，例如 每次 for 循环中 let 定义的 i 都是一个独立的个体
* let const 在同一作用域中不可以同名声明变量，var 可以

### 变量提升

变量提升只提升 Var 和 声明式函数

####var 示例：

~~~
var 预解析 只声明变量，不进行赋值
console.log(person) //输出 undefined 
var person = 123
console.log(person) //输出 123

预解析阶段
 => console.log(person) 不解析
 => var person = 123  存中声明这个变量，但不赋值 解析
 => console.log(person) 不解析 
** 因为在预解析阶段，已经声明过这个变量person，所以第一次执行为 undefined 第二次进行赋值输出123**
~~~

####function 示例：

~~~javascript
声明式函数 声明变量，并且赋值为是一个函数
fn() // 输出 我是函数体内的 一段代码
function fn() {
    console.log('我是函数体内的 一段代码')
}
fn() // 输出 我是函数体内的 一段代码

预解析阶段
 => fn() 不解析 直接过
 => function fn() {
    console.log('我是函数体内的 一段代码')
	} 解析，声明这个变量fn 并赋值为是一个函数
 => fn() 不解析
    
** 声明式函数在预解析过程中与 var 不同, 会提前声明，并会赋值为是一个函数 所以第一次调用正常输出
~~~

### weakMap weakSet

* weakMap 实例不会妨碍垃圾回收，所以非常适合保存关联元数据，它只接受对象作为键名，弱引用，不受垃圾回收机制影响 不会造成泄露
* weakSet 实例 可以用来给对象做标签，内容接收对象 弱引用，也不受垃圾回收机制影响

### 如何避免内存泄漏

* 尽可能少的创建全局变量
* 手动清除定时器
* 合理使用闭包
* 清除 DOM 引用
* 弱引用

###Vue 双向数据绑定的原理

 vue数据双向绑定是通过数据劫持 结合 发布订阅模式来实现的， Vue2版本和Vue3实现双向数据绑定的区别在于数据劫持，Vue2使用 Object.defineProperty  进行数据劫持，Vue3使用 proxy 进行数据劫持（注：  Object.defineProperty 默认递归）proxy兼容问题

### 输入url到渲染发生了什么

参考<https://www.pianshen.com/article/75981198679/>

#### 详细版

 在浏览器中输入url会经历域名解析、建立TCP连接、发送http请求、资源解析等步骤 

1. 浏览器会开启一个线程来处理这个请求，对URL分析如果是 http 协议就按照 Web 方式来处理
2. 调用浏览器内核中的对应方法，比如 Webview 中的 loadURL 方法
3. 通过 DNS 解析获取网址中的 IP 地址， 设置 UA 等信息发出第二个 GET请求
4. 进行HTTP 协议会话，客户端发送报文（请求报头）
5. 进入到Web 服务器 上的 Web Server ， 如 Apache、Tomcat、Node.Js 等服务器
6. 进入部署好的后端应用，如 PHP Java JavaScript Python 等，找到对应的请求处理
7. 处理技术回馈报头，此处如果浏览器访问过，缓存上有对应资源，会与服务器最后修改时间对比，一致返回304
8. 浏览器开始下载 html 文档（响应报头，状态码200），同时使用缓存
9. 文档树建立，根据标记请求所需 MIME 类型的文件（比如 css、js），同时设置了cookie
10. 页面开始渲染 Dom JS根据 DOM API 操作 DOM，执行事件绑定等，页面显示完成

#### 简洁版

* 浏览器根据请求的 URL 交给 DNS 域名解析，找到真实 IP， 向服务器发起请求
* 服务器交给后台处理完成后返回数据，浏览器接收文件（html js css 图像等）
* 浏览器对加载到的资源 进行语法解析，建立相应的内部数据结构（html 中的 DOM）
* 载入 解析到的资源文件，渲染页面，完成

### 箭头函数和普通函数的区别

* 普通函数可以有匿名函数，也可以有具体名函数，但是箭头函数都是匿名函数

* 箭头函数不能用于构造函数，不能使用new

* 箭头函数中的this的指向不同

  ​	在普通函数中，this总是指向调用它的对象，如果用作构造函数，this指向创建的对象实例

  	1. 箭头函数本身不创建this，也可以说箭头函数本身没有this，但是它在声明时可以捕获所在上下文的this供自己使用。注意： this 一旦被捕获，就不会发生改变
   2. 结合 call apply 方法使用，箭头函数结合 call apply 方法调用一个函数时，之传入一个参数对 this 没有影响。 
   3. 箭头函数不绑定 arguments， 取而代之的用 ... 运算符解决

  * 其他区别
    1. 箭头函数不能 Generator 函数，不能使用 yeild 关键字
    2. 箭头函数不具有 prototype 原型对象
    3. 箭头函数不具有 super
    4. 箭头函数不具有new target

* 总结：

  1. 箭头函数的 this 永远指向其上下文的 this ，任何方法都改变不了其指向，如 call、bind、 apply
  2. 普通函数的 this 指向调用它的那个对象

  ### symbol干啥用的

  symbol类型是为了解决属性名冲突的问题的，顺带还具备模拟私有属性的功能。

  创建 symol 变量最简单的方法是用 symbol 函数。 symbol 变量有两点比较特殊

  	 它可以作为对象属性名，只有字符串和 symbol类型才能用作对象属性名
  	 没有两个 symbol 的值是相同的。

### this 指向

个人：不看函数怎么定义，就看函数怎么调用

* 正常函数调用，非严格模式下，this 指向 window ， 严格模式下，指向 undefined
* 作为对象方法调用，this 通常情况下指向调用的对象
* 构造函数指向 实例对象
* 定时器 指向 window
*  事件处理函数，事件绑定在谁身上， this 就指向谁
* 自调用函数， this 指向window

### 找出数组最大值

~~~java
const person = [1, 2, 3, 4, 5]
console.log(Math.max(...person))
    
//sort
const person = [1, 2, 3, 4, 5]+
const max = person.sort((a, b) => {return a -b})[person.length - 1]
console.log(max)
~~~



### Object 的原型链还有吗

​	原型链指向所属构造函数的prototype, Object.prototype 是顶级原型，它的原型链为 null ，这是JavaScript 很特殊的一个对象，它就是原型链的顶端



###import和require哪个是运行时编译，哪个是加载时编译

import 是 编译时加载 require 是 运行时加载

import 和 require 的区别

	* 两者遵循的模块化规范不一样，require 遵循的是 CommonJS 的规范，import 是 ES6 规范
	* require中 CommonJS默认非严格模式  import 中 ES6 默认采用严格模式
	* import 是 编译时加载 require 是 运行时加载

### import和require是同步还是异步，分别可以写在代码的什么位置

同步，import 由于是编译时加载，所以import 命令会提升到整个模块的头部，静态分析， require 执行时加载,放在所需使用的前列即可 



### Promise 红绿灯

1. ~~~javascript
   let red = () => {
               console.log('红色')
           }
           let green = () => {
               console.log('绿色')
           }
           let yellow = () => {
               console.log('黄色')
           }
   
           //切换执行函数
           function carryOut(color, timer) {
               return new Promise((resolve, reject) => {
                   setTimeout(() => {
                       color()
                       resolve()
                   }, timer)
               })
           }
           /* 递归 */
           let recursion = new Promise((resolve, reject) => {
               resolve()
           })
   
           function fn(recursion) {
             recursion.then(() => {
               return carryOut(red, 5000)
             }).then(() => {
               return carryOut(green, 3000)
             }).then(() => {
               return carryOut(yellow, 2000)
             }).then(() => {
               return fn(recursion)
             })
           }
           fn(recursion)
   ~~~

2. ~~~javascript
   // Generator 方法实现Promise 红绿灯
   var tic = function(timmer, str){
             return new Promise(function(resolve, reject) {
               setTimeout(function() {
                 console.log(str)
                 resolve(1)
               }, timmer)	
             }) 
           }
           function *gen(){
               yield tic(3000, 'red')
               yield tic(1000, 'green')
               yield tic(2000, 'yellow')
           } 
           var iterator = gen()
           var step = function(gen, iterator){
               var s = iterator.next()
               if (s.done) { 
                   step(gen, gen())
               } else { 
                   s.value.then(function() { 
                       step(gen, iterator)
                   })
           } 
           }
           step(gen, iterato)
   ~~~

   

### 发布订阅

~~~javascript
class Dep {
            constructor(callback) {
                this.subs = [];
                //每个主题更新数据的方法不一样，所以需要传进来
                this.callback = callback;
            }
            addSub(sub) {
                this.subs.push(sub);
                return this;
            }
            notify() {
                //通知此主题所有订阅者更新数据
                this.subs.forEach(item => item.update(this.callback))
            }
        }
        //订阅者（Watcher）
        class Sub {
            constructor(val) {
                this.val = val
            }
            update(callback) {
                this.val = callback(this.val);
                //相当于更新视图操作
                console.log(this.val);
            }
        }
        //发布者
        class Pub {
            constructor() {
                this.deps = [];
            }
            addDep(dep) {
                this.deps.push(dep)
            }
            removeDep(dep) {
                let index = this.deps.indexOf(dep);
                if (index !== -1) {
                    this.deps.splice(index, 1);
                    return true
                } else {
                    return false
                }
            }
            publish(dep) {
                //发布某个主题的更新
                this.deps.forEach(item => item == dep && item.notify());
            }
        }

        //新建主题， 并向主题中增加订阅者
        let dep1 = new Dep(item => item * item);
        dep1.addSub(new Sub(1)).addSub(new Sub(2)).addSub(new Sub(3));
        //新建发布者， 并向发布者中增加主题
        let pub = new Pub();
        pub.addDep(dep1);
        //发布者发布， 通知所有主题的所有订阅者更新
        pub.publish(dep1);
        //发布者发布， 通知所有主题的所有订阅者更新
        pub.publish(dep1);
        console.log("===========================");
        //新建主题， 并向主题中增加订阅者
        let dep2 = new Dep(item => item + item);
        dep2.addSub(new Sub(1)).addSub(new Sub(2)).addSub(new Sub(3));
        //向发布者中增加主题
        pub.addDep(dep2);
        //发布者发布， 通知所有主题的所有订阅者更新
        pub.publish(dep2);
        //发布者发布， 通知所有主题的所有订阅者更新
        pub.publish(dep2);
~~~

### 数组扁平化

1. ~~~java
   let person = [1, [2, [3, [4]]]]
           let ARRAY = []
           function fn(person, ARRAY) {
               for (var i = 0; i < person.length; i++) {
                   if (Array.isArray(person[i])) {
                       fn(person[i], ARRAY)
                   } else {
                       ARRAY.push(person[i])
                   }
               }
           }
           fn(person, ARRAY)
           console.log(ARRAY)
   ~~~

2. ~~~java
   /*
               转换字符串 join分割
   */
      let person = [1, [2, [3, [4]]]]
      console.log(person.join(',').split(','))
   ~~~

3. ~~~java
   /* 
               flat方法参数无穷大
   */
     let person = [[12, 21], [1, 2, 3], [2, 3, 4]]
     console.log(person.flat(Infinity))
   ~~~

4. ~~~java
   let person = [[12, 21], [1, 2, 3], [2, 3, 4]]
   function turn(arr) {
               return [].concat.apply([], arr)
           }
     console.log(turn(person))
   ~~~

### 数组去重

~~~java
方法1
let person = [1, 1, 2, 2, 3, 1, 2, 1, 3]
console.log(new Set([...person]))
    
方法2
let person = [1, 1, 2, 2, 3, 1, 2, 1, 3]
console.log(Array.form(new Set(person)))
    
方法3
双重 for 循环
let person = [1, 1, 1, 2, 3, 4, 5, 6]

function fn(person) {
    for (var i = 0; i < person.length - 1; i++) {
      for (var j = i + 1; j < person.length - 1; j++) {
        if (person[i] == person[j]) {
          person.splice(j, 1)
          j--
        }
      }
    }
    return person
  }
console.log(fn(person))
    
方法4 使用Map数据结构
    function person(arr) {
          let map = new Map()
          let array = new Array()
          for(var i = 0; i < arr.length; i++) {
            if(map.has(arr[i])) {
              map.set(arr[i], true)
            } else {
              map.set(arr[i], false)
              array.push(arr[i])
            }
          }
          return array
        }
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, 	NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}]
console.log(person(arr))
~~~

###deepClone

浅拷贝：拷贝的是对象的指针，修改内容互相影响

深拷贝：整个对象拷贝到另一个内存中，修改内容互不影响

~~~javascript
/* deepClone1 */
      let obj = {
        name: '张三',
        gender: '女',
      }
      let person = JSON.parse(JSON.stringify(obj))
      person['age'] = 18
      console.log(person)
      console.log(obj)

/* deepClone2 */
function copy(obj) {
        let newobj = null
        if(typeof(obj) == 'object' && obj !== null) {
          newobj = obj instanceof Array ? [] : {}
          for(var i in obj) {
            newobj[i] = copy(obj[i])
          }
        } else {
          newobj = obj
        }
        return newobj
      }
      console.log(copy(obj))

/* 浅层深拷贝 */
let person = Object.assign({}, obj)
      person['age'] = 18
      console.log(obj)
      console.log(person)
~~~

### 数字转换货币

~~~javascript
	function money(sum) {
        return sum.split('').reverse().reduce((a, b, c) => {
            return ((c % 3) ? b : (b + ',')) + a
        })
    }
~~~



### 单例模式

~~~java

~~~



### CDN的作用，如何让所有的用户及时看到新版本，而不是缓存

代理缓存，加速节点。内容缓存提供网站浏览内容，方便用户就近访问， 内容减缓内容进行缓存负载平衡及访问控制，提升用户浏览速度和用户体验

​	具体作用：

```java
	1. 节省骨干网带宽、降低带宽的需求量、节约成本
```
  		2. 加速功能，解决有雨用户访问量大造成的服务器过载问题
  		3. 克服网站分布不均，降低网站建设和维护成本
  		4. 提高网络稳定性
  		5. 可以隐藏源站，可以防御网络攻击

如何让所有的用户及时看到新版本，而不是缓存：React 打包完成后，css，js名字都添加了has值，所以不存在缓存的问题，index.html放到服务器中的时候，index.html可能在服务器端可能是有缓存的， 使用的是nginx服务器，因此通过对nginx配置 ，使用 no-cache或 no-store 



### 前端性能优化

####根据代码的codeReView修改代码，修改不合理的代码，如：async中罗列5个await，而应该使用 Promise

#### 小程序根据业务拆包，强化小程序的编译速度

#### 回流与重绘

回流必将引起重绘，重绘不一定会引起回流。

重绘：当页面中元素样式的改变并不影响它在文档流中的位置时（例如：color、bacnkground-color  visibility 等）浏览器会将新样式赋予给元素并重新绘制它，这个过程称为重绘

回流：当Render Tree 中部分或全部元素的尺寸、结构、或某些属性发生改变时，浏览器重新渲染部分或者全部文档的过程称为回流。

会导致回流的操作

1. 页面首次渲染
2. 浏览器窗口大小发生改变
3. 元素尺寸或位置发生改变 元素内容变化
4. 元素字体大小变化
5. 添加或删除可见的 DOM 元素
6. 激活 CSS 伪类
7. 查询某些属性或调用某些方法

性能影响 回流比重绘的代价更高

#### 图片、组件懒加载

####节流防抖

#### css放在头部，js放在底部

因为先加载html 再加载 css ，会让用户第一时间看到的页面是没有样式的，用户体验，放在 head 标签里的 css 文件和 js 文件都会阻塞渲染

js 文件也不是不可以放在头部，加 defer 属性

#### 使用字体图标代替图片图标

####if-else 对比 switch

 当判断条件数量越来越多时，越倾向于使用 switch 而不是 if-else。 

####不要覆盖原生方法

#### 降低 CSS 选择器的复杂性

#### 减少作用域链的查找

#### 打包优化

~~~java
1. 拆包 externals dllPlugin
2. 提取公共包 commonChunkPlugin或splitChunks
3. 缩小范围 各种loader配置include和exclude，noParse跳过文件
4. 开启缓存 各种loader开启cache
5. 多线程加速 happypack或thead-loader
6. tree-shaking ES模块分析，移除死代码
7. Scope Hoisting ES6模块分析，将多个模块合并到一个函数里，减少内存占用，减小体积，提示运行速度
8. webpack长缓存优化
    
1. js文件使用chunkhash，不使用hash
2. css文件使用contenthash，不使用chunkhash，不受js变化影响
3. 提取vendor，公共库不受业务模块变化影响
4. 内联webpack runtime到页面，chunkId变化不影响vendor
5. 保证module Id稳定，不使用数字作为模块id，改用文件内容的hash值，使用HashedModuleIdsPlugin，模块的新增或删除，会导致其后面的所有模块id重新排序，为避免这个问题
6. 保证chunkhash稳定，使用webpack-chunk-hash，替代webpack自己的hash算法。webpack自己的hash算法，对于同一个文件，在不同开发环境下，会计算出不用的hash值，不能满足跨平台需求。
~~~

### 协商缓存和强缓存

协商缓存： 向服务器发送请求，服务器会根据这个请求的request header的一些参数来判断是否命中协商缓存，如果命中，则返回304状态码并带上新的response header通知浏览器从缓存中读取资源 

强缓存： 不会向服务器发送请求，直接从缓存中读取资源，在chrome控制台的network选项中可以看到该请求返回200的状态码 

两者共同点：都是从客户端缓存中读取资源。 区别：强缓存不会发起请求，协商缓存会发请求

###继承

#### 例子

* 我们之前说，构造函数（类）是对一类行为的描述
* 那么我们类这个概念其实也很抽象
* 比如：
  * 我们说 `国光` / `富士` 都是 苹果的品种，那么我们就可以写一个 `苹果类` 来实例化很多品种出来
  * 而 `苹果` / `梨` 这些东西都是水果的一种，那么我们就可以写一个 `水果类`
  * 说过的统一特点就是 `甜` / `水分大` ，而不同的水果有不同的特征
  * 那么我们就可以让 `苹果类` 来继承 `水果类` 的内容，然后再用 `水果类` 去实例化对象
  * 那么实例化出来的就不光有 `苹果类` 的属性和方法，还有 `水果类` 的属性和方法

#### 常见的继承方式

* 我们有一些常见的方法来实现或达到继承的效果

* 我们来定义一个父类

  ~~~javascript
  function Person() {
      this.name = 'Jack'
  }
  Person.prototype.sayHi = function () {
      console.log('hello')
  }
  ~~~

* 这个 `Person` 构造函数为父类

* 让其他的构造函数来继承他

* 当别的构造函数能够使用他的属性和方法的时候，就达到了继承的效果

##### 原型继承

* 原型继承，就是在本身的原型链上加一层结构

~~~java
function fn() {}
fn.prototype = new Person()
~~~

##### 借用构造函数继承

把父类构造函数体借用过来使用以下而已

~~~javascript
function fn() {
    Person.call(this)
}
~~~

##### 组合式继承

组合式继承就是将 `原型继承` 和 `借用构造函数继承` 组合起来

~~~javascript
function person(name) {
    this.name = name
  }
  person.prototype.sayHi = function () {
    console.log('person')
  }

  function fn(age, name) {
    this.age = age
    person.call(this, name)
  }
  fn.prototype = new person()
  let res = new fn(15, 'Jack')
  console.log(res)
~~~

##### ES6 继承

固定写法，简单

~~~javascript
// 下面表示创造一个fn 类，继承自 Person 类
class Student extends Person {
    constructor () {
        // 必须在 constructor 里面执行一下 super() 完成继承 
        super()
    }
}
~~~





### 为什么hooks 不可以写在if里面

遵守这条规则，你就能确保 Hook 在每一次渲染中都按照同样的顺序被调用。这让 React 能够在多次的 useState 和 useEffect 调用之间保持 hook 状态的正确。（官方的说法）

### 闭包

* 闭包是我们函数的一种高级使用方式
* 在聊闭包之前先回顾一下   **函数**

#### 函数的两个阶段

* 函数有两个阶段
  1. 创建阶段
  2. 调用阶段

#### 函数的创建阶段

1. 开辟一个 **存储空间**
2. 把函数体内代码一摸一样的放在这个空间内（不解析变量）
3. 把 **存储空间** 的地址给函数名

#### 函数调用阶段

1. 按照 函数名 的地址找到函数的 **存储空间** 
2. 形参赋值
3. 预解析
4. 将函数 **存储空间** 的代码拿出来执行（才解析变量）

#### 重新定义函数调用阶段

1. 按照 函数名 找到函数的 **存储空间**
2. 形参赋值
3. 预解析
4. 在内存中开辟一个 **执行空间**
5. 将函数中 **存储空间** 中的代码拿出来在 **执行空间** 内执行 
6. 执行完，将内存中的 **执行空间** 销毁

~~~javascript
function fn() {
    console.log('这是 fn')
}
fn()
~~~

+ 函数执行的时候会开辟一个 **执行空间** （我们暂且叫它 xxfn）
+ 我们的代码 “这是 fn” 就是在这里执行
+ 执行完毕后，xxfn 销毁

#### 函数执行空间

- 每一个函数都有一个 **存储空间**
- 但是每一次调用都产生一个完全不一样的 **执行空间**
- 并且 **执行空间** 会在函数执行完后销毁，**存储空间**不会
  * 我们可以通过一些方法让这个空间不销毁
  * **闭包** 就是利用了这个不会被销毁的函数执行空间

#### 函数不销毁的 执行空间

- 函数的 **执行空间** 在函数执行完后就会销毁
- 但是一旦函数内部返回了一个 **引用数据类型** ，且在 **函数外部有变量接收**的情况下
- 这个 **执行空间** 不会被销毁

~~~java
function fn() {
    const obj = {
        name: 'Jack',
        gender: '男',
        age: 18
    }
    return obj
}
const o = fn()
~~~

*  函数 fn 执行时，会产生一个 **执行空间** （我们暂且叫他fnxx）
* 代码 在 fnxx 中执行
* 在 fnxx 这个空间空 声明了一个 对象空间（暂且叫它fnxx1）
* 在 fnxx 这个 **执行空间** 中把 fnxx1 地址返回了
* 函数外部 `0` 接收的是一个对象的地址没错
  * 但是是一个在 `fnxx` 函数执行空间中的 `fnxx1` 对象地址
  * 因为 `o` 变量一直在和这个对象地址关联着，所以 `fnxx` 这个空间一直不会销毁
* 等到什么时候，执行一句代码 `o = null`

#### 闭包

##### 不销毁的空间

* 闭包的第一个条件就是利用了不销毁空间的逻辑

* 但是不是返回一个 **对象数据类型**

* 而是返回了一个 **函数数据类型**

* ~~~java
  function fn(){
      return function () {}
  }
  const n = fn()
  ~~~

  * 变量 `n` 接收的就是 函数 **fn的函数执行空间**

#### 内部函数引用外部函数的变量

* 涉及到两个函数

* 内部函数要查看或者使用外部函数的变量

  ~~~java
  function fn() {
      const sum = 100
      // a没有意义，为做笔记
      return function a() {
          console.log(sum)
      }
  }
  const f = fn()
  ~~~

  * `fn` 函数执行的时候，会产生一个 `ooff00`  的 **函数执行空间**
  * `ooff00` 里面 定义了一个函数名为 `a` 的 **存储空间** `ooff11`
  * 变量 `f` 接收的就是 `ooffoo` 里面的 `ooff11`
  * 就是 `ooff00` 是不会销毁的执行空间
  * 因为 `ooff00` 不会被销毁的，所以在他里面定义的 变量 `sum` 也不会被销毁
  * 将来 `fn()` 执行的时候，就能够访问到 `sum `

#### 闭包的特点

* 为什么叫做特点，因为闭包的每一个点都是优点同时也是缺点
  1. 作用域空间不会被销毁
     * 优点：因为空间不会被销毁，所以内部的变量也不会 不难z被销毁，增加了变量的生命周期
     * 缺点：因为不会被销毁，所以会一直占用内存，多了就会造成内存溢出，而内存溢出就会造成内存泄漏
  2. 可以利用闭包在一个函数外部访问一个函数内部的变量
     * 优点：可以在外部访问内部函数的变量
     * 缺点：必须时刻保持引用，导致函数执行栈不会被销毁
  3. 保护变量私有化
     * 优点：可以把一些变量放到函数里面，避免全局变量污染
     * 缺点：要使用闭包才可以访问到，不是很方便

#### 闭包概念

* 有一个 A 函数，在函数内部返回一个 B 函数
* 在 A 函数外部有变量引用 B 函数
* B 函数内部访问这 A 函数内部的私有变量
* 三个条件缺一不可

###webpack实现一个自己的loder



### http 状态码

* 200 请求成功。一般用于 GET  与 POST请求
* 201  创建成功。成功请求并创建了新的资源
* 204 服务端已经成功处理了请求，但是没有任何数据返回
* 301 永久重定向
* 302 临时重定向
* 304 使用缓存
* 305 使用代理
* 400 客户端请求的语法错误，服务器无法理解
* 401 请求要求用户的身份认证
* 403 服务器拒绝执行此请求
* 404 查询不到对应页面
* 500 服务器内部错误
* 501 服务器不支持请求的功能，无法完成请求
* 503 服务器 当前不可用 维护或者过载

### GET POST 区别

1. GET 请求会被浏览器主动缓存 POST请求不会被浏览器主动缓存
2. GET 请求长度受限 POST 请求理论上没有限制，除非手动设置
3. GET 参数类型有限制，只接受 ASCLL码的格式 POST参数 没有显示
4. GET 请求明文发送 POST密文发送

### CORS 请求头

~~~javascript
Access-control-Allow-origin: 地址
Access-conreol-Allow-Methods：POST,GET
Access-control-Allow-Credenttials:true
Access-control-Allow-Headers: Origin,Content-Type, Accept, token, x-Request-with
~~~



### UDP 和 TCP的 区别

1. TCP 连接 UDP 不连接
2. TCP 可靠 UDP 不可靠 丢包不重发
3. TCP 传输效率低 UDP 传输效率高
4. TCP 传输速度慢 UDP 传输速度快

* 电子邮件 远程终端接入 万维网 文件传输 使用 TCP 
* 域名转换 文件传输 网络管理 远程文件服务器

总结：TCP 和 UDP 优缺点无法简单地、绝对地去做比较；TCP 用于传输层有必要实现可靠传输的情况；UDP主要用于那些对高速传输 和 实时性有 较高要求的通信或广播通信。按需使用

### HTTP 和 HTTPS 的基本概念

1. https 协议需要到ca申请证书，一般免费证书较少，因而需要一定的费用
2. http 是超文本传输协议，https则是具有安全性的ssl 加密传输协议
3. http 和 https 使用的是完全不同的连接方式，用的端口号也不一样，前者是80，后者是443
4. http 的连接很简单，是无状态的； HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全 

### JS 延迟加载的方式

defer 和 async 或者 动态创建 DOM方式、 按需导入 js

### MVC MVVM

#### MVC

控制器完成业务逻辑后，要求 Model 改变状态

Model 将心的数据发送到 View ， 用户得到反馈

所有的通信都是单向的。

MVC 优缺点：

优点：

* 耦合性低
* 复用性高
* 部署快
* 可维护性高
* 有利于软件工程化管理

缺点：

* **不适合小型中等规模的应用程序**
* 增加了系统结果和实现的复杂性
* View 和 Model 之间界限不清，什么样的逻辑都是界面逻辑，什么样的逻辑是业务逻辑，很难定义清楚。没有明确的定义
* View 的变化不能由 Model 控制， 即 Observer模式不足以支持福娃的用户交互。这其实要求 VC 之间要有依赖。牵一发而动全身，数据，显示不分离，控制器，Model联系过于紧密。

#### MVVM

在MVVM架构下，View 和 Model 之间其实并没有直接的联系，而是通过ViewModel进行交互，Model 和 ViewModel 之间的交互是双向的， 因此View 数据的变化会同步到Model中，而Model 数据的变化也会立即反应到View 上。

ViewModel 通过双向数据绑定把 View 层和 Model 层连接了起来，ViewModel里面包含DOM Listeners和Data Bindings，DOM Listeners和Data Bindings是实现双向绑定的关键。DOM Listeners监听页面所有View层DOM元素的变化，当发生变化，Model层的数据随之变化；Data Bindings监听Model层的数据，当数据发生变化，View层的DOM元素随之变化。

优缺点：

优点：

* 低耦合
* 可复用性
* 可测试性

缺点：

* 学习成本高
* debug 调试困难

#### MVVM 应用场景

* 针对具有复杂交互逻辑的前端应用
* 提供基础的架构抽象
* 通过 Ajax 数据持久化，保证前端用户体验

### 解决移动端 1px 的问题

transform：scaleY（）

##React

#### React 模式

* **legacy 模式：** `ReactDOM.render(, rootNode)`。这是当前 React app 使用的方式。当前没有计划删除本模式，但是这个模式可能不支持这些新功能。
* **blocking 模式：** `ReactDOM.createBlockingRoot(rootNode).render()`。目前正在实验中。作为迁移到 concurrent 模式的第一个步骤。
* **concurrent 模式：** `ReactDOM.createRoot(rootNode).render()`。目前在实验中，未来稳定之后，打算作为 React 的默认开发模式。这个模式开启了*所有的*新功能。

#### React 错误边界

默认情况下，若一个组件在渲染期间发生错误，会导致整个组件树全部被卸载

错误边界：这是一个组件，该组件会捕获到渲染期间的子组件发生的错误，并由能力阻止错误继续传播

#### React 为什么需要合成事件

1. **兼容浏览器监听写法**
2. 避免大量节点绑定事件占用让内存，将事件委托到 document 上， 有统一的事件处理函数，等事件冒泡到 document 上时， React 从 target 节点往上遍历父元素，判断有没有元素绑定对应时间，有则触发该事件
3. 事件回调函数处理完成后需要清空 state 队列中的更新， 重新渲染视图

####React 中 Redux 使用的 三大原则

* Single Source of Truth （唯一数据源）
* State is read-only （状态只读）
* Changes are made with pure function （数据的改变必须通过纯函数完成）

####React setState

1. 调用 setstate 不会立即更新
2. 所有组件使用的是同一套更新机制，当所有组件 didmount 后，父组件 didimount , 然后执行更新
3. 更新时会把每个组件的更新合并，每个组件只会触发一次更新的生命周期
4. **不同的模式下，setState 的更新是不同的**

##### 为什么 有时连续两次 setState 只有一次生效

* 直接传递对象的 **setState** 会被合并成一次
* 使用函数传递 **setState** 不会被合并**（源码中 batchedUpdates 优化，命中异步判断批处理）**

##### setState 同步异步

​	**legacy** 模式命中batchedUpdates 时异步

​	**legacy** 模式未命中 batchdUpdates 时同步

​	**concurrent** 模式下全部异步

##### 钩子函数和合成事件中

* 在 react 的生命周期和合成事件中， react    仍然处于它的更新机制中， 这时 isBranchUpdate 为 true
* 按照上述过程，这时无论调用多少次 setState ，都不会执行更新，而是将要更新的 state 存入 _pendingStateQueue, 将要更新的组件 存入 dirtyComponent。
* 当上一次更新机制执行完毕，以生命周期为例，所有组件，即最顶层组件 didmount 后会将 isBranchUpdate 设置为 false 。 这时将执行之前积累的 steState。

##### 异步函数和原生事件中

由执行机制看，`setState`本身并不是异步的，而是如果在调用`setState`时，如果`react`正处于更新过程，当前更新会被暂存，等上一次更新执行后在执行，这个过程给人一种异步的假象。

在生命周期，根据JS的异步机制，会将异步函数先暂存，等所有同步代码执行完毕后在执行，这时上一次更新过程已经执行完毕，`isBranchUpdate`被设置为false，根据上面的流程，这时再调用`setState`即可立即执行更新，拿到更新结果。

##### componentDidMount 调用 setState

~~~
在 componentDidMount 中，你可以立即调用 setState。他将会触发一次额外的渲染，但是它将在浏览器刷新屏幕之前发生。这保证了在此情况下即使 render 将会调用两次，用户也不会看到中间状态。谨慎使用这一模式。因为它常导致性能问题。在大多数情况下，你可以在 constructor 中使用复制初始状态来代替，然而，有些情况下必须这样，比如像模态框和工具提示框。这时。你需要先测量这些 DOM 节点，才能渲染依赖尺寸或者位置的某些东西。
~~~

以上是官方文档的说明，不推荐直接在 componentDidMount 直接调用 setState ，由上面的分析：**compoentDidMount** 本身处于一次更新中，我们又调用了一次 setState ，就会在未来再进行一次 render ，造成不必要的性能浪费，大多数情况可以设置初始值来搞定。

当然在 **compoentDidMount** 我们可以直接调用接口， 再回调中去修改 state ，这是正确的做法。



当 state 初始值依赖 dom 属性时，在 compoentDidMount 中 setState 是无法避免的。

##### componentWillUpdate    componentDidUpdate

这两个生命周期中不能调用 **setState** 

在它们里面调用 **setState** 会造成死循环，导致程序崩溃

##### 推荐使用方式

在调用 setState 时使用函数传递 state 值， 在回调函数中获取最新更新后的 state

### React Fiber

在 React 16 之后发布的一种 React 核心算法，**React Fiber 是对核心算法的一次重新实现**（官方说法）。在之前使用的是 diff 算法。

在之前的 React 中，更新过程是同步的，这可能会导致性能问题。

当 React 决定要加载或者更新组件树时，会做很多事，比如调用各个组件的生命周期函数，计算和对比 Virtual DOM ，最后更新 DOM 树， 这整个过程都是同步进行，也就是说只要一个加载或者更新过程开始，中途不会中断。因为 Javascript 单线程的特点，如果组件树很大的时候， 每个同步任务耗时太长，就会出现卡顿。

React Fiber 的方法其实很简单 一一分片， 把一个耗时长的任务分成很多小片， 每个小片的运行时间很短，虽然总时间依然很长，但是在每个小片执行完成后，都给其他任务一个执行的机会，这样唯一的线程就不会被独占，其他任务依然有运行的机会。



#### Redux 执行流程

* **store** 通过 **reducer** 创建了初始状态
* **view** 通过 **store.getState** 获取到了 **store** 中保存的 state 挂载在了自己的状态上
* 用户产生了操作，调用了 **actions** 方法
* **actions** 的方法被调用， 创建了带有标识性新的 action
* **actions** 将 **action** 通过调用 **store.dispatch** 方法发送到了 **reducer**
* **reducer** 接收到 **action** 并根据标识信息判断之后返回新的 **state**
* **store** 的 state 被 **reducer** 更改为新的 state 的时候， store.subscribe 方法里的回调函数会执行， 此时就可以通知 view 去重新获取 state 

####React Hooks useState 过时闭包问题

使用箭头函数 保存 state 数据 进行更改

## theory 

###typeof

 typeof 在我们判断 一个 object 类型的时候，他只会告诉我们是 object ， 而不能具体细致的告诉我们是哪一种 object 。

js 在底层存储变量的时候，会在变量的机器码的地位1 - 3 位存储其类型信息

* 000 对象
* 010 浮点数
* 100 字符串
* 110 布尔
* 1 整数

但是，对于 **null** 和 **undefined** 来说这两个的值的信息存储是有点特殊的。

1. ***null*** ：所有机器码均为0
2. ***undefinded*** ： 用-2^30整数来表示

所以，typeof 在判断 null 的时候就出现了问题， 由于 null 的 所有机器码均为0， 因为当做了对象来看待。

因此再用 typeof 的来判断变量类型的时候， 我们需要注意，最好用 typeof 来判断基础数据类型， 避免对 null 的判断。

###instanceof

其实 **instanceof** 主要的作用就是判断一个实例是否属于某种类型

~~~
let person = function () {}
let nilole = new person()
nilole instanceof person // true
~~~

当然， instanceof 也可以判断一个实例是否是其父类型 或者 祖先类型 的实例

其实，instanceof 主要的实现原理就是只要右边变量的 prototype 在左边的变量的原型链上即可。

因此 instanceof 在查找的过程中会遍历左边变量的原型链， 直到找到右边变量的 prototype ，如果查找失败， 则会返回 false ， 告诉我们左边变量并非右边变量的实例。

###总结

我们使用 typeof 来判断基本数据类型是 ok 得， 不过需要注意当用 typeeof 来判断 null 类型时得问题， 如果想要判断一个对象得具体类型可以考虑用 instanceof， 但是instanceof 也可能判断不准确，比如一个数组，它可以被 instanceof 判断为 Object 。 所以我们想要比较准确的判断的对象实例的类型时， 可以采取 ***Object.prototype.toString.call*** 方法。

### 前端路由的两种模式： hash 模式 和 history 模式

* hash 模式： 监听浏览器地址 hash 值变化，执行相应的js切换网页
* history模式：利用 history API 实现 url 地址改变， 网页内容改变

他们的区别最明显的就是 hash 会在浏览器地址后面增加 # 号， 而 history 可以自定义地址

#####hash 模式

使用 window .location .hash 属性及窗口的 onhashChange 事件， 可以实现监听浏览器的地址hash 值变化， 执行相应的js 切换网页。

##### history 模式

window.history 属性 指向 History 对象，它表示当前窗口的浏览历史，当发生改变时，只会改变页面的路径，不会刷新页面

History 对象保存了当前窗口访问过的所有页面网址。通过 history.length 可以得出当前窗口一共访问过几个网址。

由于安全原因，浏览器不允许脚本读取这些地址，但是允许在地址之间导航。

history 致命的缺点就是当改变页面地址后，强制刷新浏览器时，（如果后台没做准备的话）会报错， 因为刷新是拿当前地址去请求服务器的， 如果服务器中没有响应的响应，会出现404页面

### Promise

Promise 是异步编程的一种解决方案，比传统的解决方案 -- callback 更合理更加强大，它由社区最早提出和实现， ES6 将其写进了语言标准， 统一了用法，原生提供了 Promise 对象

所谓 Promise ， 简单说就是一个容器， 里面保存着某个未来才会结束的事件， 从它可以获取异步操作的消息。

#### Promise 对象有以下两个特点

1. 对象的状态不受外界影响。Promise 对象代表一个异步操作， 有三种状态 pending 进行中、 fulfilled  已成功和 rejected 已失败。只有异步操作的结果，可以决定当前是哪一种状态， 人喝其他操作都无法改变这个状态。这也是 Promise 的由来。
2. 一旦状态发生改变，就不会再变，人喝时候都可以得到这个结果。Promise 对象的状态改变，只有两种可能；从 进行中变为 成功 或者 从进行中变为失败。只要这两种情况发生，状态就凝固了， 不会再变了，会一直保持这个状态。

#### Promise.all

~~~
const p = Promise.all([p1, p2, p3])
~~~

上面的代码中，Promise 方法接收一个数组作为参数， p1, p2, p3都是Promise 的实例。也可以不是数组， 但是必须具有 Iterator 接口， 且返回的每个成员都是 Promise 实例

p 的状态由 P1 p2 p3决定，分成两种情况

1. 只有 p1 p2 p3 的状态都变成 fulilled p的状态才会变成 fulfilled ，此时， p1 p2 p3的返回值成为一个数组，传递给 p 的回调函数。
2. 只要 p1 p2 p3之中还有一个被 rejected p的状态就变成 rejected ，这是第一个被 reject的实例的返回值，会传递给p的回调函数

#### Promise.race

Promise.race 方法同样是将多个 Promise 实例， 包装成一个新的 Promise 实例

~~~
const p = Promise.race([p1, p2, p3])
~~~

上面的代码中，只要 p1 p2 p3之中有一个实力率先改变状态，p的状态就跟着改变。那个率先改变的Promise 实例的返回值，就传递给 p 的回调函数。（竞速器）

####Promise.allSettled

Promise.allSettled 方法接收一组 Promise 实例为参数，包装成一个新的 Promise 实例。只有等到所有的参数实例都返回结构，不管是 fulfilled 还是 rejected ，包装实例才会结束。该方法 由 ES2020引入

####Promise.any

 该方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例返回。只要参数实例有一个变成`fulfilled`状态，包装实例就会变成`fulfilled`状态；如果所有参数实例都变成`rejected`状态，包装实例就会变成`rejected`状态。 



## webpack

#### webpack 五大核心原则

* entry 入口文件
* output 出口
* loader 加载器
* plugins 插件
* mode 模式

#### 常用的loder

* style-loader 将css 添加到DOM 的内联样式标签style里
* css-loader 允许将css 文件通过 require 的方式引入，并返回css 代码
* less-loader 处理less
* sass-loader 处理sass
* babel-loader 用babel 来转换ES6 文件到 ES5

总结：

* loader 是 webpack 核心，用于对模块的源代码进行转换
* loader 支持链式调用，从左到右执行，支持同步函数或异步函数

####常用的 plugin

* ignore-plugin 忽略部分文件
* html-webpack-plugin 简化 HTML 文件创建 （依赖于html-loader）
* web-webpack-plugin 可方便地为单页应用输出 HTML，比html-webpack-plugin 好用
* terser-webpack-plugin 支持压缩ES6
* mini-css-extract-plugin 分离样式文件

