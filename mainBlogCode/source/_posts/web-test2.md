---
title: 前端面试题 ---- [1]
date: 2021-01-20 11:14:48
tags: Web
---
<meta name="referrer" content="no-referrer"/>

## JS相关问题
#### 1.说说你对闭包的理解
#### 2.请你谈谈Cookie的弊端
#### 3.浏览器本地存储
#### 4.web storage和cookie的区别
## CSS相关问题
#### 5.display:none和visibility:hidden的区别？
#### 6.CSS中 link 和@import 的区别是？
#### 7.position:absolute和float属性的异同？
#### 8.介绍一下box-sizing属性？
#### 9.CSS 选择符有哪些？哪些属性可以继承？优先级算法如何计算？CSS3新增伪类有那些？
#### 10.position的值， relative和absolute分别是相对于谁进行定位的？
#### 11.CSS3有哪些新特性？
#### 12.XML和JSON的区别？
#### 13.对BFC规范的理解？
#### 14.解释下 CSS sprites，以及你要如何在页面或网站中使用它。？
#### 15.说说你对语义化的理解？
#### 16.Doctype作用? 严格模式与混杂模式如何区分？它们有何意义?
#### 17.你知道多少种Doctype文档类型？
#### 18.HTML与XHTML——二者有什么区别?
#### 19.常见兼容性问题？
#### 20.解释下浮动和它的工作原理？清除浮动的技巧？
#### 21.DOM操作——怎样添加、移除、移动、复制、创建和查找节点。
#### 22.html5有哪些新特性、移除了那些元素？如何处理HTML5新标签的浏览器兼容问题？如何区分 HTML 和 HTML5？
#### 23.iframe的优缺点？
#### 24.线程与进程的区别
#### 25.你如何对网站的文件和资源进行优化？
#### 26.请说出三种减少页面加载时间的方法。
#### 27.null和undefined的区别？
#### 28.new操作符具体干了什么呢?
#### 29.js延迟加载的方式有哪些?
#### 30.javascript对象的几种创建方式
#### 31.javascript继承的6种方法
#### 31.GET和POST的区别，何时使用POST
## HTML相关问题

----
#### 1.说说你对闭包的理解
使用闭包主要是为了设计私有变量和方法。
闭包的优点：可以避免全局变量的污染
闭包的缺点：闭包会常驻内存，会增大内存使用量，使用不当容易造成内存泄漏。
* 闭包的三个特性：
1. 函数嵌套函数
2. 函数内部可以引用函数外部的参数和变量
3. 参数和变量不会被垃圾回收机制回收
#### 2.请你谈谈Cookie的弊端
cookie虽然在持久保存客户端数据提供了方便，分担了服务器存储的负担，但是仍有很多压力。
1. 每个特定域名下最多生成20个cookie
* IE6或更低版本最多20个
* IE7或更新版本最多50个
* FireFox最多50个
* Chrome和Safari没有硬性限制
2. Cookie的最大字节不会超过4kB。
优点： 
1. 通过良好的编程，控制保存在cookie中session对象的大小
2. 通过加密和安全传输技术（SSL），减少cookie被破解的可能行
3. 在cookie中存储不敏感的数据，及时被破解也不会造成大风险。
4. 控制cookie的生命期，即使被盗，也可能是过期的cookie
缺点：
1. cookie有长度和数量的限制：
2. 安全性问题：如果cookie被盗，即使加密之后获取不到session信息，重复提交cookie也可达到目的。
3. cookie有些状态不能保存在客户端：例如为防止重复提交表单会在服务端保存一个计时器，如果是在客户端的计时器，就毫无意义。
#### 3.浏览器本地存储
sessionStorage 和 localStorage
sessionStorage：会话级别的存储，这些数据只能在同一个会话页面中才能访问，会话结束，数据就会随之销毁。
localStorage：持久化的本地存储，除非手动删除，否则不会删除。

#### 4.web storage和cookie的区别
cookie：
* 大小、数量受限制
* 每次请求一个新的页面cookie都会被发送过去，无形中浪费了带宽。
* 需要指定作用域，不接受跨域调用。

其他：
* web storage有setItem,getItem,removeItem,clear等方法， 而cookie需要自己封装。
* cookie的作用适用于与服务器进行交互，作为http规范的一部分存在，而web storage仅用于本地存储。

## CSS相关问题
#### 5.display:none和visibility:hidden的区别？
都是-元素被隐藏
* display:none;文档布局中不会分配空间。
* visibility:hidden; 文档布局中会分配空间。
#### 6.CSS中 link 和@import 的区别是？
* link属于HTML标签，而@import是css提供的。
* 页面开始加载时，同时加载link标签，而import引用的css会在页面加载之后加载。
* @import只在IE5以上才能识别，而link是HTML标签，无兼容问题。
* link方式的样式权重高于@import的权重
#### 7.position:absolute和float属性的异同？
* 共同点：
内联元素设置`float`和`absolute`会使元素脱离文档流，并且可以设置其宽高。
* 不同点：
float会占据元素位置，absolute会脱离文档流覆盖其他元素。
#### 8.介绍一下box-sizing属性？
box-sizing 主要用来控制盒模型的解析模式
默认值：content-box。
* `content-box`元素的宽高由border+padding+content的宽度和高度决定，设置的width/height指的是content的宽/高。
* `border-box`让元素维持IE传统盒模型,设置的width/height指的是border+padding+content。
#### 9.CSS 选择符有哪些？哪些属性可以继承？优先级算法如何计算？CSS3新增伪类有那些？
* 选择器
id选择器（#id）
类选择器 (.class)
标签选择器 (div)
相邻元素选择器 (div + p)
子选择器 (ul > li)
后代选择器 （li a）
通配符选择器 (*)
属性选择器 （a[rel = "external"]）
伪类选择器 （a:hover li:nth-child）

* 可继承的样式
font-size font-family color  text-indent(首行文本锁进)
* 不可继承的样式
border padding margin width height
* 优先级就近原则 同权重情况下样式定义最近者为准
载入样式以最后载入的定位为准
* ！import > 内联 > id > calss > tag
* CSS3新增伪类举例：
P:first-of-type选择属于其父元素的首个p元素 （每一个p元素）
P:last-of-type选择属于其父元素的最后一个p元素 （每一个p元素）
P:only-of-type选择属于其父元素的唯一一个p元素 （每一个p元素）
P:only-child选择属于其父元素的唯一的元素 （每一个p元素）
P:nth-child(2)选择属于其父元素的第二个子元素 （每一个p元素）
:enabled  :disabled 控制表单控件的禁用状态。
:checked        单选框或复选框被选中。
在CSS3中唯一引入的伪元素是::selection

#### 10.position的值， relative和absolute分别是相对于谁进行定位的？
* absolute： 绝对定位
  生成最近一级的定位的不是static 的父元素来进行定位
* relative 相对定位
  相对于其在普通流中的位置进行定位。
* static 默认值
  生成在正常的流中
* fixed 绝对定位
  相对于浏览器窗口
#### 11.CSS3有哪些新特性？
* 圆角border-radius,阴影box-shadow
* 文字加特效text-shadow,线性渐变gradient,旋转transform
* transform:rotate(9deg) scale(0.85,0.90) translate(0px,-30px) skew(-9deg,0deg);//旋转,缩放,定位,倾斜
* 媒体查询：@media 可以针对不同的屏幕尺寸设置不同的样式，特别是如果你需要设置设计响应式的页面。
* 多栏布局：多栏布局可以将一个元素中的内容分为两栏或多栏显示，并且确保各栏中内容底部对齐column-count、column-width、column-gap、column-rule
* border-image
#### 12.XML和JSON的区别？
* 数据体积方面
JSON相对体积小，传输速度快
* 数据交互方面。
JSON与javascript交互更方便，更容易解析处理，更好的数据交互
* 数据描述方面
JSON对数据的描述性更差
* 传输速度方面
JSON传输速度更快

#### 14.解释下 CSS sprites，以及你要如何在页面或网站中使用它。？
CSS sprites（雪碧图）是把网页中一些背景图整合到一张图片文件中，再利用css的background-postion、background-image、background-repeat的组合进行背景定位，以减少图片请求的开销，
因为请求比较耗时，请求虽然可以并发，但是可以限制，一般浏览器是6个。
对于未来而言，就不需要这么做了，以为有了http2.

## HTML相关问题
#### 15.说说你对语义化的理解？
* 去掉或者丢掉样式可以让页面呈现出清晰的结构
* 有利于SEO;和搜索引擎建立良好沟通，有助于爬虫抓取更多的有效信息
* 方便其他设备的解析（移动设备、阅读器）
* 便于维护和开发
#### 16.Doctype作用? 严格模式与混杂模式如何区分？它们有何意义?

<!DOCTYPE> 声明位于文档中的最前面，用来告诉浏览器以何种模式来渲染文档！
严格模式和JS运作模式是 以浏览器支持的最高标准运行
混杂模式中，页面以宽松的向后兼容的方式显示。模拟老式浏览器的行为防止站点无法正常工作。
DOCTYPE不存在或者不正确会导致文档以混杂模式呈现。

#### 17.你知道多少种Doctype文档类型？
有三个版本：严格版本、过度版本、基于框架的HTML文档。
HTML规定了三种文档类型： Strict、Transitional 以及 Frameset
XHTML 1.0 规定了三种 XML 文档类型: Strict、Transitional 以及 Frameset。
Standards （标准）模式（也就是严格呈现模式）用于呈现遵循最新标准的网页，而 Quirks
 （包容）模式（也就是松散呈现模式或者兼容模式）用于呈现为传统浏览器而设计的网页。

 #### 18.HTML与XHTML——二者有什么区别？
 * 所有的标签必须要有相应的一个结束标签
 * 标签名小写
 * 所有的XML标记都需要合理嵌套
 * 所有属性都需要“”嵌套
 * 把所有<和&特殊符号用编码表示
 * 所有属性都需要赋值
 * 不要在注释内容中使“--”
 * 图片必须有说明文字

 #### 19.常见兼容性问题？
 * png24位的图片在iE6浏览器上出现背景，解决方案是做成PNG8.也可以引用一段脚本处理.

* 浏览器默认的margin和padding不同。解决方案是加一个全局的*{margin:0;padding:0;}来统一。

* IE6双边距bug:块属性标签float后，又有横行的margin情况下，在ie6显示margin比设置的大。 

* 浮动ie产生的双倍距离（IE6双边距问题：在IE6下，如果对元素设置了浮动，同时又设置了margin-left或margin-right，margin值会加倍。）
  #box{ float:left; width:10px; margin:0 0 0 100px;} 

 这种情况之下IE会产生20px的距离，解决方案是在float的标签样式控制中加入 ——_display:inline;将其转化为行内属性。(_这个符号只有ie6会识别)

*  渐进识别的方式，从总体中逐渐排除局部。 

  首先，巧妙的使用“9”这一标记，将IE游览器从所有情况中分离出来。 
  接着，再次使用“+”将IE8和IE7、IE6分离开来，这样IE8已经独立识别。

  css
      .bb{
       background-color:#f1ee18;/*所有识别*/
      .background-color:#00deff9; /*IE6、7、8识别*/
      +background-color:#a200ff;/*IE6、7识别*/
      _background-color:#1e0bd1;/*IE6识别*/ 
      } 

*  IE下,可以使用获取常规属性的方法来获取自定义属性,
   也可以使用getAttribute()获取自定义属性;
   Firefox下,只能使用getAttribute()获取自定义属性. 
   解决方法:统一通过getAttribute()获取自定义属性.

* IE下,event对象有x,y属性,但是没有pageX,pageY属性; 
  Firefox下,event对象有pageX,pageY属性,但是没有x,y属性.

* 解决方法：（条件注释）缺点是在IE浏览器下可能会增加额外的HTTP请求数。

* Chrome 中文界面下默认会将小于 12px 的文本强制按照 12px 显示, 
  可通过加入 CSS 属性 -webkit-text-size-adjust: none; 解决.

* 超链接访问过后hover样式就不出现了 被点击访问过的超链接样式不在具有hover和active了解决方法是改变CSS属性的排列顺序:
L-V-H-A :  a:link {} a:visited {} a:hover {} a:active {}

* 怪异模式问题：漏写DTD声明，Firefox仍然会按照标准模式来解析网页，但在IE中会触发怪异模式。为避免怪异模式给我们带来不必要的麻烦，最好养成书写DTD声明的好习惯。现在可以使用[html5](http://www.w3.org/TR/html5/single-page.html)推荐的写法：`<doctype html>`

* 上下margin重合问题
ie和ff都存在，相邻的两个div的margin-left和margin-right不会重合，但是margin-top和margin-bottom却会发生重合。
解决方法，养成良好的代码编写习惯，同时采用margin-top或者同时采用margin-bottom。
* ie6对png图片格式支持不好(引用一段脚本处理)

#### 20.解释下浮动和它的工作原理？清除浮动的技巧？
浮动元素脱离文档流，不占据空间。浮动元素碰到包含它的边框或者浮动元素的边框停留。

1.使用空标签清除浮动。
   这种方法是在所有浮动标签后面添加一个空标签 定义css clear:both. 弊端就是增加了无意义标签。
2.使用overflow。
   给包含浮动元素的父标签添加css属性 overflow:auto; zoom:1; zoom:1用于兼容IE6。
3.使用after伪对象清除浮动。
   该方法只适用于非IE浏览器。具体写法可参照以下示例。使用中需注意以下几点。一、该方法中必须为需要清除浮动元素的伪对象中设置 height:0，否则该元素会比实际高出若干像素；
#### 21.DOM操作——怎样添加、移除、移动、复制、创建和查找节点。
```
1）创建新节点
  createDocumentFragment()    //创建一个DOM片段
  createElement()   //创建一个具体的元素
  createTextNode()   //创建一个文本节点
2）添加、移除、替换、插入
      appendChild()
      removeChild()
      replaceChild()
      insertBefore() //在已有的子节点前插入一个新的子节点
3）查找
      getElementsByTagName()    //通过标签名称
      getElementsByName()    //通过元素的Name属性的值(IE容错能力较强，会得到一个数组，其中包括id等于name值的)
      getElementById()    //通过元素Id，唯一性
```
#### 22.html5有哪些新特性、移除了那些元素？如何处理HTML5新标签的浏览器兼容问题？如何区分 HTML 和 HTML5？
* HTML5 现在已经不是 SGML 的子集，主要是关于图像，位置，存储，多任务等功能的增加。

* 拖拽释放(Drag and drop) API 
  语义化更好的内容标签（header,nav,footer,aside,article,section）
  音频、视频API(audio,video)
  画布(Canvas) API
  地理(Geolocation) API
  本地离线存储 localStorage 长期存储数据，浏览器关闭后数据不丢失；
  sessionStorage 的数据在浏览器关闭后自动删除

  表单控件，calendar、date、time、email、url、search  
  新的技术webworker, websocket, Geolocation

* 移除的元素

纯表现的元素：basefont，big，center，font, s，strike，tt，u；

对可用性产生负面影响的元素：frame，frameset，noframes；

支持HTML5新标签：

* IE8/IE7/IE6支持通过document.createElement方法产生的标签，
  可以利用这一特性让这些浏览器支持HTML5新标签，

  浏览器支持新标签后，还需要添加标签默认的样式：

* 当然最好的方式是直接使用成熟的框架、使用最多的是html5shim框架
   <!--[if lt IE 9]> 
   <script> src="http://html5shim.googlecode.com/svn/trunk/html5.js"</script> 
   <![endif]--> 
如何区分：DOCTYPE声明新增的结构元素功能元素

#### 23.iframe的优缺点？

1.`<iframe>`优点：

    解决加载缓慢的第三方内容如图标和广告等的加载问题
    Security sandbox
    并行加载脚本

2.`<iframe>`的缺点：


    *iframe会阻塞主页面的Onload事件；

    *即时内容为空，加载也需要时间
    *没有语意 

#### 24.线程与进程的区别

一个程序至少有一个进程,一个进程至少有一个线程. 
线程的划分尺度小于进程，使得多线程程序的并发性高。 
另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。 
线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。 
从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。

#### 25.你如何对网站的文件和资源进行优化？
期待的解决方案包括：
* 文件合并
* 文件最小化/文件压缩
* 使用CDN托管
* 缓存的使用（多个域名来提供缓存）
* 其他
#### 26.请说出三种减少页面加载时间的方法。
* 1.优化图片 
* 2.图像格式的选择（GIF：提供的颜色较少，可用在一些对颜色要求不高的地方） 
* 3.优化CSS（压缩合并css，如margin-top,margin-left...) 
* 4.网址后加斜杠（如www.campr.com/目录，会判断这个“目录是什么文件类型，或者是目录。） 
* 5.标明高度和宽度（如果浏览器没有找到这两个参数，它需要一边下载图片一边计算大小，如果图片很多，浏览器需要不断地调整页面。这不但影响速度，也影响浏览体验。 
当浏览器知道了高度和宽度参数后，即使图片暂时无法显示，页面上也会腾出图片的空位，然后继续加载后面的内容。从而加载时间快了，浏览体验也更好了。） 
* 6.减少http请求（合并文件，合并图片）。

#### 27.null和undefined的区别？
* 相同点
表示”无”的对象
* 不同点
null装换为数值时是0，undefined转换为数值时是NaN

当声明的变量还未初始化时，变量的默认值是undefined。

null表示尚未存在的对象，常用来表示函数企图返回一个不存在的对象。
undefined表示"缺少值",就是此处应该有一个值，但是还没有定义。
* 典型用法：
1. 变量被声明了，但是没有赋值，就等于undefined.
2. 调用函数的时候，该提供的参数没有提供，该参数等于undefined.
3. 对象没有赋值的属性，该属性值为undefined。
4. 函数没有返回值时，默认是undefined.

null表示没有值，即该处不该有值
* 典型用法：
1. 作为函数的参数，表示该函数的参数不是对象。
2. 作为对象原型链的终点。

#### 28.new操作符具体干了什么呢?
1. 创建了空对象，并且this变量引用该对象，同时还继承了该属性的原型。
2. 属性和方法被加入到this引用的对象中。
3. 新创建的对象由this所引用，并且最后隐式的返回this。
```
var obj = {}
obj._proto_ = Base.prototype;
Base.call(obj);
```
#### 29.js延迟加载的方式有哪些?
defer和async、动态创建DOM方式（创建script，插入到DOM中，加载完毕后callBack）、按需异步载入JS

#### 30.javascript对象的几种创建方式
1，工厂模式
2，构造函数模式
3，原型模式
4，混合构造函数和原型模式
5，动态原型模式
6，寄生构造函数模式
7，稳妥构造函数模式
#### 31.javascript继承的6种方法
1，原型链继承
2，借用构造函数继承
3，组合继承(原型+借用构造)
4，原型式继承
5，寄生式继承
6，寄生组合式继承
#### 31.GET和POST的区别，何时使用POST？
GET：一般用于信息获取，使用URL传递参数，对所发送信息的数量也有限制，一般在2000个字符
POST：一般用于修改服务器上的资源，对所发送的信息没有限制。

GET方式需要使用Request.QueryString来取得变量的值，而POST方式通过Request.Form来获取变量的值，
也就是说Get是通过地址栏来传值，而Post是通过提交表单来传值。

然而，在以下情况中，请使用 POST 请求：
无法使用缓存文件（更新服务器上的文件或数据库）
向服务器发送大量数据（POST 没有数据量限制）
发送包含未知字符的用户输入时，POST 比 GET 更稳定也更可靠