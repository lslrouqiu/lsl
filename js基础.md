js基本知识点
1. [变量类型](#变量类型):值类型和引用类型
    - 判断类型的方式
    - 深拷贝浅拷贝
2. 

# 变量类型 
-  值类型：变量本身就是含有赋予给它的数值的，它的变量本身及保存的数据都存储在栈的内存块当中,包括Number,String,Boolean,Undefied,Null,Symbol这6种
- 引用类型：引用类型当然是分配到堆上的对象或者数据变量，根据官方一点的解释就是引用类型的变量只包括对其所表示的数据引用，包括数组，对象，Function,Error，RegExp等

## 判断数据类型
- ### typeof
typeof只能区分值类型，对引用类型无能为力，只能区分函数function，可返回的类型包括以下 7 种：number、boolean、symbol、string、object、undefined、function 
 - 对于基本类型，除 null 以外，均可以返回正确的结果, 对于 null ，返回 object 类型。
 - 对于引用类型，除 function 以外，一律返回 object 类型。
 - 对于 function 返回  function 类型。

```
typeof NaN      //"number" NaN表示特殊的非数字值
typeof null     //"object" null是空指针，并没有指向任何一个地址
```
- ### instanceof 
instanceof 是用来判断 A 是否为 B 的实例，表达式为：A instanceof B，如果 A 是 B 的实例，则返回 true,否则返回 false。instanceof 检测的是原型,B是否在A的原型链上。instanceof 只能用来判断两个对象是否属于实例关系， 而不能判断一个对象实例具体属于哪种类型。
> instanceof 操作符的问题在于，它假定只有一个全局执行环境。如果网页中包含多个框架，那实际上就存在两个以上不同的全局执行环境，从而存在两个以上不同版本的构造函数。如果你从一个框架向另一个框架传入一个数组，那么传入的数组与在第二个框架中原生创建的数组分别具有各自不同的构造函数。


```
var iframe = document.createElement('iframe');
document.body.appendChild(iframe);
xArray = window.frames[0].Array;
var arr = new xArray(1,2,3); // [1,2,3]
arr instanceof Array; // false
```
针对数组的这个问题，ES5 提供了 Array.isArray() 方法 。该方法用以确认某个对象本身是否为 Array 类型，而不区分该对象在哪个环境中创建。
Array.isArray() 本质上检测的是对象的 [[Class]] 值，[[Class]] 是对象的一个内部属性，里面包含了对象的类型信息，其格式为 [object Xxx] ，Xxx 就是对应的具体类型 。对于数组而言，[[Class]] 的值就是 [object Array] 。


- ### constructor
当一个函数 F被定义时，JS引擎会为F添加 prototype 原型，然后再在 prototype上添加一个 constructor 属性，并让其指向 F 的引用。当执行 var f = new F() 时，F 被当成了构造函数，f 是F的实例对象，此时 F 原型上的 constructor 传递到了 f 上，因此 f.constructor == F
> 1. null 和 undefined 是无效的对象，因此是不会有 constructor 存在的，这两种类型的数据需要通过其他方式来判断。
> 2. 函数的 constructor 是不稳定的，这个主要体现在自定义对象上，当开发者重写 prototype 后，原有的 constructor 引用会丢失，constructor 会默认为 Object.因为 prototype 被重新赋值的是一个 { }， { } 是 new Object() 的字面量，因此 new Object() 会将 Object 原型上的 constructor 传递给 { }，也就是 Object 本身。因此，为了规范开发，在重写对象原型时一般都需要重新给 constructor 赋值，以保证对象实例的类型不被篡改。
- ### toString 
toString() 是 Object 的原型方法，调用该方法，默认返回当前对象的 [[Class]] 。这是一个内部属性，其格式为 [object Xxx] ，其中 Xxx 就是对象的类型。
对于 Object 对象，直接调用 toString()  就能返回 [object Object] 。而对于其他对象，则需要通过 call / apply 来调用才能返回正确的类型信息。

```
Object.prototype.toString.call('') ;   // [object String]
Object.prototype.toString.call(1) ;    // [object Number]
Object.prototype.toString.call(true) ; // [object Boolean]
Object.prototype.toString.call(Symbol()); //[object Symbol]
Object.prototype.toString.call(undefined) ; // [object Undefined]
Object.prototype.toString.call(null) ; // [object Null]
Object.prototype.toString.call(new Function()) ; // [object Function]
Object.prototype.toString.call(new Date()) ; // [object Date]
Object.prototype.toString.call([]) ; // [object Array]
Object.prototype.toString.call(new RegExp()) ; // [object RegExp]
Object.prototype.toString.call(new Error()) ; // [object Error]
Object.prototype.toString.call(document) ; // [object HTMLDocument]
Object.prototype.toString.call(window) ; //[object global] window 是全局对象 global 的引用
```

## 浅拷贝，深拷贝
深拷贝和浅拷贝的示意图大致如下：

![enter image description here](http://www.haorooms.com/uploads/images/copysq.png)

浅拷贝只复制指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存。但深拷贝会另外创造一个一模一样的对象，新对象跟原对象不共享内存，修改新对象不会改到原对象。

对于引用类型，当我们将a赋值给b，然后更改b中的属性，a也会随着变化。
也就是说a和b指向了同一块内存，所以修改其中任意的值，另一个值都会随之变化，这就是浅拷贝。

如果给b放到新的内存中，将a的各个属性都复制到新内存里，就是深拷贝。
也就是说，当b中的属性有变化的时候，a内的属性不会发生变化。
### 浅拷贝：
1. 赋值引用
2. Object.assign():用于将所有可枚举的属性的值从一个或多个源对象复制到目标对象,它将返回目标对象。Object.assign()拷贝的只是属性值，假如源对象的属性值是一个指向对象的引用，它也只拷贝那个引用值。所以Object.assign()只能用于浅拷贝或是合并对象。
3. Array.prototype.slice
### 深拷贝
1. 迭代递归法

2. 序列化反序列化法
> 1. 拷贝的对象的值中如果有函数,undefined,symbol则经过JSON.stringify()序列化后的JSON字符串中这个键值对会消失
> 2. 无法拷贝不可枚举的属性，无法拷贝对象的原型链
> 3. 拷贝Date引用类型会变成字符串
> 4. 拷贝RegExp引用类型会变成空对象
> 5. 对象中含有NaN、Infinity和-Infinity，则序列化的结果会变成null
> 6. 无法拷贝对象的循环应用(即obj[key] = obj)stringfy()会检测循环引用

[深拷贝](https://juejin.im/post/5bc1ae9be51d450e8b140b0c)解决递归爆栈和循环引用的问题
#### 正则表达式的克隆 

```
const reFlags = /\w*$/
function cloneRegExp(regexp) {
  const result = new regexp.constructor(regexp.source, reFlags.exec(regexp)) //regexp.constructor 就是 RegExp
  result.lastIndex = regexp.lastIndex //lastIndex 表示每次匹配时的开始位置。 使用正则对象的 test 和 exec 方法，而且当修饰符为 g 或 y 时， 对 lastIndex 是有影响的。
  return result
}

var regexp = /xyz/imgyus;
regexp.flags
// => "gimsuy"
regexp.toString()
// => "/xyz/gimsuy"
```
一个正则对象可以大致分成两部分，源码(source) 和修饰符(flags)。比如，/xyz/gim 的 source 是 "xyz"，而其 flags 是 "gim"。
clone 正则时，还要 clone 其 lastIndex。

# 原型和原型链


```
graph LR
Person构造函数-.实例化.-> 实例person;
实例person -- __proto__ --> Person.prototype
Person构造函数--prototype-->  Person.prototype
Person.prototype--constructor-->  Person构造函数
Person构造函数 -- __proto__ --> Function.prototype
Function构造函数 -- prototype --> Function.prototype
Function.prototype -- constructor --> Function构造函数
Function构造函数-.实例化.->Person构造函数
Function.prototype-- __proto__ --> Objcect.prototype
Person.prototype-- __proto__ --> Objcect.prototype
Objcect构造函数 -- prototype --> Objcect.prototype
Objcect.prototype -- constructor --> Objcect构造函数
Objcect构造函数--__proto__ --> Function.prototype
Function构造函数-.实例化.->Objcect构造函数
Objcect.prototype--__proto-->null
```
