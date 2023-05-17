# **JAVASCRIPT BASIS**

  
## **HTML 中的 Javascript**
---
  
### **一、\<script> 标签的属性**
* src：外部 javascript 文件的 URL 地址（可以是与当前网站不同域的地址），设置了该属性则浏览器不会解析标签内部代码。
* type：脚本语言的内容类型（ MIME 类型，默认为 text/javascript），还可以被设置为 module 表示该脚本支持 import&export 的 ES6 模块化标准。
* defer：延迟执行脚本，遇到该属性的 script 标签且此标签有 src 属性值时，浏览器会立即下载外部 javascript 文件（下载过程不会阻塞 HTML 的解析），但下载完成之后会延迟执行（等待 HTML 解析完成之后再执行，如果有多个 defer 属性的 script 标签，会按照顺序执行）。
* async：异步执行脚本，遇到该属性的 script 标签且此标签有 src 属性值时，浏览器会立即下载外部 javascript 文件（下载过程不会阻塞 HTML 的解析），下载完成之后开始执行（不会等待 HTML 解析完成，如果有多个 async 属性的 script 标签，执行时机和标签顺序无关）。
  
### **二、HTML 与 \<script> 标签的解析机制**
* 在正常情况下 HTML 文档会从上至下依次解析，遇到 script 标签则会等待标签内容解析完成之后再解析标签下面的内容（如果是外部文件，下载的过程也会阻塞其他 HTML 内容的解析）。
* 如果 script 标签有 defer 和 src 属性的值，则会跳过该标签去解析其他内容，同时浏览器会下载该标签的文件。待 HTML 解析完成且标签文件下载完成，浏览器会开始执行 script 标签中的内容。如果有多个该类型的标签，则会按照标签的顺序依次执行。所有的执行都会在 DOMContentLoaded 事件之前。
* 如果 script 标签有 async 和 src 属性的值，浏览器同样会跳过该标签去解析其他内容，同时开始下载标签的文件。与 defer 不同的是，async 会在文件下载完成之后立即开始执行，此时 HTML 不一定解析完成，执行的过程会与 HTML 解析同步进行（有待理清具体的执行机制），且执行时机不会与其他 async 属性的标签顺序有关联。async 脚本会在页面 load 事件前执行，但可能会在 DOMContentLoaded 之前或之后。
  
### **三、其他**
* 在 HTML 中 \<script> 标签不可以忽略结束标签。
```html
<!-- 以下写法为正确写法 🙆 -->
<script src="a.js"></script>

<!-- 以下写法在 HTML 中是错误的写法 🙅 -->
<script src="a.js" />

```
* 使用行内 \<script> 时需要注意不可以出现 \</script> 的字符。
```html
<script>
    console.log("</script>") //🙅 错误写法，会导致<script>标签提前结束
    console.log("<\/script>") //🙆 正确写法
</script>
```
* 可以通过向 DOM 中添加 \<script> 标签的方式来动态加载 js 文件，不过这样的方式可能会影响资源网络加载的性能，因为这种方式获取的资源对浏览器预加载器是不可见的，可以通过在 \<head> 标签中声明 \<link rel="preload" href="***.js"> 的方式告诉预加载器需要预先加载那些资源。
* \<noscript> 标签可以显示当浏览器不支持 javascript 脚本时页面需要展示的内容。
  
---
## **变量与数据类型**
---
  

### **一、var 与 let const**
* var 声明的变量是函数级作用域，let const 声明的变量为块级作用域。
* var 声明的变量在作用域内会有声明“提升”的机制，let const 不会。
* var 可以出现冗余声明，let const 在作用域内不允许出现冗余声明。
* var 在全局作用域中声明的变量等同于挂载到 window 上的属性，let const 不是。
```js
var name = "lee";
let age = 21;

console.log(window.name===name) // 🙆 true
console.log(window.age===age) // 🙅 false
```
* 在 for 循环中，let 会在每个迭代循环创建独立变量实例，var 则始终会是同一个变量
```js
for(let i = 0; i < 2; i++){
    setTimeout(()=>void console.log(i),0);
}
// 输出 0,1


for(var i = 0; i < 2; i++){
    setTimeout(()=>void console.log(i),0);
}
// 输出 1,1
```

### **二、Javascript 中的数据类型**
* String
* Number
* Boolean
* Object
* Null
* Undefined
* Symbol

### **三、typeof 操作符**
```js

const str = "str";
const objStr = new String('str')
const num = 0;
const ObjNum = new Number(10);
const bool = true;
const obj = {};
const fn = () => {};
const sym = Symbol('sym');
const nul = null;
const undef = undefined;
const nan = NaN;

console.log(
    typeof str === "string",
    typeof objStr === "object",
    typeof num === "number",
    typeof ObjNum === "object",
    typeof bool === "boolean",
    typeof obj === "object",
    typeof nul === "object",
    typeof sym === "symbol",
    typeof fn === "function",
    typeof undef === "undefined",
    typeof nan === "number"
)
```

### **四、null 与 undefined**
* 在语意上，undefined 表示未初始化值的变量，null 表示空对象指针（可以在定义一个未知的对象时使用 null 来初始化）。
* typeof undefined === "undefined"，typeof null === "object"。
* Number(undefined) === NaN，Number(null) === 0。
* 某个变量的值是 undefined 与这个变量未定义是有区别的，未定义的变量只能执行 typeof 操作（或者在条件语句中执行赋值操作，等同于给 window 的某个属性赋值），否则会报错。
```js
const a;

console.log(typeof a) // undefined
console.log(typeof b) // undefined

console.log(c) // 🙅 ReferenceError

if(true){
    d = "d" //等同于 window.d = "d"
}
console.log(d) // d
```

### **五、Boolean()**
```js
/**
 * 除了以下值会被转换成 false，其他所有值都会被转换成 true
*/
console.log(Boolean(false)) //false
console.log(Boolean('')) //false
console.log(Boolean(0)) //false
console.log(Boolean(NaN)) //false
console.log(Boolean(null)) //false
console.log(Boolean(undefined)) //false
```

### **六、Number()**
```js
console.log(Number()) //0

/** boolean */
console.log(Number(true)) //1
console.log(Number(false)) //0

/** object */
console.log(Number(null)) //0
console.log(Number({})) // NaN
console.log(Number({
    valueOf: ()=>10
})) //10
console.log(Number({
    valueOf: ()=> NaN,
    toString: ()=> "100"
})) //100

/** undefined */
console.log(Number(undefined)) //NaN

/** string */
console.log(Number("")) //0
console.log(Number("10")) //10
console.log(Number("10.1")) //10.1
console.log(Number("10.1.1")) //NaN
console.log(Number("aa")) //NaN
```

### **七、NaN**
* typeof NaN === "number"。
* NaN 不与任何数据相等，包括他自身。
* NaN 与任何数据进行比较都返回 false。
* NaN 与任何数据进行计算都返回 NaN。
* 可以通过 isNaN() 判断一个数据是否是NaN。

### **八、parseInt() parseFloat()**
* parseInt() parseFloat() 会将非字符串和数字的数据转换成字符串再进行转换处理。
* parseInt() 可以传递第二个参数指定转换的进制数。
```js
console.log(parseInt()) //NaN
console.log(parseInt("")) //NaN
console.log(parseInt("123.456abc")) //123
console.log(parseFloat("123.456.789abc")) //123.456
console.log(parseInt("abc")) //NaN
console.log(parseInt({toString:()=>"100"})) //100
console.log(parseInt("0xA"))// 10（十六进制）
console.log(parseInt("A"，16))// 10（十六进制）
```

### **九、String() toString()**
* 除了 null 和 undefined，其他所有数据都有 toString 方法。
* number 数据的 toString 方法可以传递参数指定转换的进制数。
* String 会默认调用参数的 toString 方法来进行转换，null undefined 会直接转换成 "null" "undefined"。

### **十、摸板字面量标签函数**
```js
const strTemp = (strings,...args) => {
    console.log(strings,args);
    return 'strTemp';
};
const a = "a", b="b", c="c";

const str =  strTemp`${a} - ${b} - ${c}`; //[""," - "," - ",""],["a","b","c"]

console.log(str) //strTemp

```

### **十一、Symbol**
* Symbol() Symbol.for() Symbol.keyFor()
```js
const symA1 = Symbol.for('a');
const symA2 = Symbol.for('a');

console.log(symA1===symA2); //true

const symA3 = Symbol('a');

const symA4 = Symbol('a');

console.log(symA1===symA3); //false
console.log(symA3===symA4); //false

console.log(Symbol.keyFor(symA1)) // a
console.log(Symbol.keyFor(symA3)) // undefined
```

* Symbol 作为属性
```js
const obj = {
    a: "a",
    [Symbol('a')]: "symbol a"
}

console.log(Object.getOwnPropertyNames(obj)) // ["a"]
console.log(Object.getOwnPropertySymbols(obj)) // [Symbol(a)]
console.log(Reflect.ownKeys(obj)) // ["a",Symbol(a)]
```

### **十二、Object 实例的属性和方法**
* constructor 用于构建当前实例的函数。
* hasOwnProperty 用于判断当前实例上是否有某个属性（非通过原型继承的属性）。
* isPrototypeOf 用于判断当前对象是否为另一个对象的原型。
* propertyIsEnumerable 用于判断某个属性是否可以使用 for-in 枚举。
* toLocalString toString valueOf

---
## **操作符**
---

### **一、递增｜递减**
```js
let a = 1; //a === 1;
let a1 = ++a; //a1 === 2,a===2
let a2 = a++; //a2 === 2,a===3
let a3 = --a; //a3 === 2,a===2
let a4 = a--; //a4 === 2,a===1
let a5 = ++a+1; //a5 === 3,a===2
let a6 = 1+a++; //a6 === 3,a===3
```

### **二、位操作符**
* 有符号整数使用 32 位的前 31 位表示整数数值（从左往右数），第 32 位表示数值的符号（0 表示 +，1 表示 -）。
* 正值以真正的二进制格式存储，即31位中的每一位都代表2的幂。第一位表示2^0，第二位表示2^1，依次类推。
* 负值首先以其正值为基础，然后对正值的每一位进行反码，最后再加上 1
```js
// 18 的二进制存储格式
let num18 = 00000000000000000000000000010010; //等价于 10010 (2^0)*0+(2^1)*1+(2^2)*0+(2^3)*0+(2^4)*1 = 18

// -18 的二进制存储格式
// 1111 1111 1111 1111 1111 1111 1110 1110
```

* 按位非（~），返回每位数上的补数。
```js
let num1 = 18; //    二进制 0000 0000 0000 0000 0000 0000 0001 0010
let num2 = ~num1; // 二进制 1111 1111 1111 1111 1111 1111 1110 1101 等价于十进制 -19
```

* 按位与（&），每一位进行与操作。
```js
// 1 & 1 -> 1
// 1 & 0 -> 0
// 0 & 0 -> 0
// 0 & 1 -> 0

let num = 25 & 3;
// 25 -> 0000 0000 0000 0000 0000 0000 0001 1001
//  3 -> 0000 0000 0000 0000 0000 0000 0000 0011
//AND -> 0000 0000 0000 0000 0000 0000 0000 0001 等价于十进制 1
```

* 按位或（|），每一位进行或操作。
```js
// 1 | 1 -> 1
// 1 | 0 -> 1
// 0 | 0 -> 0
// 0 | 1 -> 1

let num = 25 | 3;
// 25 -> 0000 0000 0000 0000 0000 0000 0001 1001
//  3 -> 0000 0000 0000 0000 0000 0000 0000 0011
// OR -> 0000 0000 0000 0000 0000 0000 0001 1011 等价于十进制 27
```

* 按位异或（^），每一位进行异或操作。
```js
// 1 ^ 1 -> 0
// 1 ^ 0 -> 1
// 0 ^ 0 -> 0
// 0 ^ 1 -> 1

let num = 25 ^ 3;
// 25 -> 0000 0000 0000 0000 0000 0000 0001 1001
//  3 -> 0000 0000 0000 0000 0000 0000 0000 0011
//XOR -> 0000 0000 0000 0000 0000 0000 0001 1010 等价于十进制 26
```

* 左移（<<）,按照指定的位数将值的每一位(不包括符号位)向左移动。
```js
let num = 2 << 5;
//    2 -> 0000 0000 0000 0000 0000 0000 0000 0010
// << 5 -> 0000 0000 0000 0000 0000 0000 0100 0000 等价于十进制 64

let num2 = -2 << 5; // -64
```

* 有符号右移动（>>），按照指定的位数将值的每一位(不包括符号位)向右移动。
```js
let num = 64 >> 5;
//   64 -> 0000 0000 0000 0000 0000 0000 0100 0000
// >> 5 -> 0000 0000 0000 0000 0000 0000 0000 0010 等价于十进制 2

let num2 = -64 >> 5; // -2
```

* 无符号右移动（>>>），按照指定的位数将值的每一位(包括符号位)向右移动。
```js
let num = 64 >>> 5;
//   64 -> 0000 0000 0000 0000 0000 0000 0100 0000
//>>> 5 -> 0000 0000 0000 0000 0000 0000 0000 0010 等价于十进制 2

let num2 = -64 >>> 5; 
//  -64 -> 1111 1111 1111 1111 1111 1111 1100 0000
//>>> 5 -> 0000 0111 1111 1111 1111 1111 1111 1110  等价于十进制 134217726
```

### **三、布尔操作符**
* 逻辑非（!），等同于调用Boolean()函数。
* 逻辑与（&&），如果第一个值转换为布尔值为false，则直接返回第一个值，否则返回第二个值。
* 逻辑或（||），如果第一个值转换为布尔值为false，则直接返回第二个值，否则返回第一个值。

### **四、数学计算**
* NaN 参与任何计算都返回 NaN。
* Infinity * 0 === NaN。
* Infinity / Infinity === NaN。
* 0 / 0 === NaN。
* Infinity % 任何值 === NaN。
* Infinity - Infinity === NaN。

### **五、比较操作符**
* 都是字符串则比较字符编码。
* 任何一个为数字则将另一个转换为数字比较。
* 对象调用 valueOf()，如果没有则用 toString()，转换之后比较。
* 布尔值转换为数字进行比较。
* 如何与 NaN 的比较都是 false。
```js
const boo1 =  NaN < 3 // false
const boo2 = NaN >= 3 // false
```

### **六、相等操作符（==，!=）**
* 任意值为布尔值则将其转化为数值再比较。
* 字符串与数值比较时会将字符串转化为数值。
* 一个对象与一个非对象比较时会调用对象的 valueOf() 方法之后再比较。
* null == undefined。
* null 和 undefined 不能转化为其他类型的值再进行比较。
```js
console.log(null == 0) // false
console.log(undefined == 0) // false
```

## **七、逗号操作符**
```js
const num = (1,2,5,6) // num === 6 
```

---
## **语句**
---
### **一、for-in**
* for-in 语句是一种严格的迭代语句，用于枚举对象中的非符号（非 Symbol ）键属性。

### **二、for-of**
* for-of 语句是一种严格的迭代语句，用于遍历可迭代对象的元素（可以遍历 Symbol）。
* for-of 循环会按照可迭代对象的 next() 方法产生的值的顺序迭代元素。
* for-await-of 可以支持 Promise 的异步可迭代对象。

---
## **作用域与内存**
---

### **一、原始值**
* 原始值包含
```js
const str = "string" // String
const num = 1 //Number
const boo = true // Boolean
const nul = null // Null
const undef = undefined // Undefined
const sym = Symbol('symbol') // Symbol
```
* 使用 new 关键字与原始字面量的区别
```js
const str1 = "string";
const str2 = new String("string");

// 1.两个值不完全相等
console.log(str1===str2) // false
console.log(str1==str2) // true

// 2.typeof 的值不同
console.log(typeof str1) // "string"
console.log(typeof str2) // "object"

// 3.使用 new 构建的值可以设置动态属性并且读取动态属性
str1.name = "name";
str2.name = "name";
console.log(str1.name) // undefined
console.log(str2.name) // "name"
```

### **二、确定值的类型**
* string number boolean undefined symbol（Symbol()） 的字面量原始值可以直接使用 typeof 判断
* instanceof 可以判断某个变量是否是某个构造函数的实例。（需要注意所有引用类型都是 Object 的实例）。

### **三、执行上下文与作用域**
* js 中包含 ***全局作用域***、***函数作用域***、***块级作用域***。
* 每个作用域都存在一个执行上下文，上下文关联一个 ***变量对象***，在上下文中定义的所有变量和函数都存在于这个对象上。
* 当代码流进入某个上下文时，该上下文会被推入上下文栈。
* 上下文中的代码在执行的时候会创建一个 ***变量对象*** 的 ***作用域链***，这个 ***作用域链*** 决定代码在访问变量和函数时查找各个 ***上下文变量对象*** 的顺序。
* ***作用域链*** 按照上下文栈从当前上下文一直连接到全局上下文。
* 上下文执行完毕之后会退出上下文栈并销毁该上下文内定义的变量和函数，将控制权返回给上一个上下文（后入先出）。
* 全局上下文会在关闭网页时退出，所以为了促进内存回收，全局变量可以在不需要时手动销毁（赋值为 null）