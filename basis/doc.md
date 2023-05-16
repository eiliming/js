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
    typeof undef === "undefined"
)
```

### **四、null 与 undefined**
* 在语意上，undefined 表示未初始化变量，null 表示空对象指针，所以本质上 null 是一个对象（可以在定义一个未知的对象时使用 null 来初始化）。
* typeof undefined === "undefined"，typeof null === "object"。
* Number(undefined) === NAN，Number(null) === 0。
* 变量的值是 undefined 与变量未定义是有区别的，未定义的变量只能执行 typeof 操作（或者在条件语句中执行赋值操作，等同于给 window 的某个属性赋值），否则会报错。
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
console.log(Boolean(NAN)) //false
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
console.log(Number({})) // NAN
console.log(Number({
    valueOf: ()=>10
})) //10
console.log(Number({
    valueOf: ()=> NAN,
    toString: ()=> "100"
})) //100

/** undefined */
console.log(Number(undefined)) //NAN

/** string */
console.log(Number("")) //0
console.log(Number("10")) //10
console.log(Number("10.1")) //10.1
console.log(Number("10.1.1")) //NAN
console.log(Number("aa")) //NAN
```

### **七、parseInt() parseFloat()**
* parseInt() parseFloat() 会将非字符串和数字的数据转换成字符串再进行转换处理。
* parseInt() 可以传递第二个参数指定转换的进制数。
```js
console.log(parseInt()) //NAN
console.log(parseInt("")) //NAN
console.log(parseInt("123.456abc")) //123
console.log(parseFloat("123.456.789abc")) //123.456
console.log(parseInt("abc")) //NAN
console.log(parseInt({toString:()=>"100"})) //100
console.log(parseInt("0xA"))// 10（十六进制）
console.log(parseInt("A"，16))// 10（十六进制）
```

### **八、String() toString()**
* 除了 null 和 undefined，其他所有数据都有 toString 方法。
* number 数据的 toString 方法可以传递参数指定转换的进制数。
* String 会默认调用参数的 toString 方法来进行转换，null undefined 会直接转换成 "null" "undefined"。

### **九、摸板字面量标签函数**
```js
const strTemp = (strings,...args) => {
    console.log(strings,args);
    return 'strTemp';
};
const a = "a", b="b", c="c";

const str =  strTemp`${a} - ${b} - ${c}`; //[""," - "," - ",""],["a","b","c"]

console.log(str) //strTemp

```

### **十、Symbol**
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

### **十一、Object 实例的属性和方法**
* constructor 用于构建当前实例的函数。
* hasOwnProperty 用于判断当前实例上是否有某个属性（非通过原型继承的属性）。
* isPrototypeOf 用于判断当前对象是否为另一个对象的原型。
* propertyIsEnumerable 用于判断某个属性是否可以使用 for-in 枚举。
* toLocalString toString valueOf