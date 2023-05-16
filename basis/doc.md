# **JAVASCRIPT BASIS**
  
## HTML 中的 Javascript
  
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