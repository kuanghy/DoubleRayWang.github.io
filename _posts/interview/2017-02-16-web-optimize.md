---
layout: post
title: "Web 前端性能优化"
date: 2017-02-16 12:19:15 +0800
categories: 面试总结
tag: Web
---

* content
{:toc}


　　前端性能优化是个巨大的课题，如果要面面俱到的说的话，估计三天三夜说不完。所以我们就从实际的工程应用角度出发，聊我们最常遇见的前端优化问题。

　　Yslow是雅虎开发的基于网页性能分析浏览器插件，可以检测出网页的具体性能值，并且有著名的Yslow 23条优化规则，那我们就先来看看这23条。<!-- more -->

## Yslow 23条

<hr>

- **减少HTTP请求次数**

　　尽量合并图片、CSS、JS。比如加载一个页面，如果有5个 css 文件的话，那么会发出5次 http 请求，这样会让用户第一次访问你的页面的时候会长时间等待。而如果把这个5个文件合成一个的话，就只需要发出一次 http 请求，节省网络请求时间，加快页面的加载。

<hr>

- **使用CDN**

　　网站上静态资源即 css、js 全都可以使用 cdn 分发，图片亦然。

<hr>

- **避免空的 src 和 href**

　　当 link 标签的 href 属性为空、script 标签的 src 属性为空的时候，浏览器渲染的时候会把**当前页面的 URL** 作为它们的属性值，从而把页面的内容加载进来作为它们的值。所以要避免这样的疏忽。

<hr>

- **为文件头指定 Expires**

　　Exipres 是用来设置文件的过期时间的，一般对 css、js、图片资源有效。他可以使内容具有缓存性，这样下回再访问同样的资源时就通过浏览器缓存区读取，不需要再发出 http 请求。如下例子：

<img src="{{ '/styles/images/web/optimize_1.png' | prepend: site.baseurl }}" alt="Expires 优化" />

　　新浪微博的这个css文件的Expires时间是2016-5-04 09:14:14。

<hr>

- **使用gzip压缩内容**

　　gzip 能够压缩任何一个文本类型的响应，包括 html，xml，json 。大大缩小请求返回的数据量。

<hr>

- **把 CSS 放到顶部**

　　网页上的资源加载时从上网下顺序加载的，所以 css 放在页面的顶部能够优先渲染页面，让用户感觉页面加载很快。

<hr>

- **把 JS 放到底部**

　　加载 js 时会对后续的资源造成阻塞，必须得等 js 加载完才去加载后续的文件 ，所以就把 js 放在页面底部最后加载。

<hr>

- **避免使用 CSS 表达式**

　　举个css表达式的例子：

```css
 font-color: expression( (new Date()).getHours()%3 ? “#FFFFFF" : “#AAAAAA" );
```

　　这个表达式会持续的在页面上计算样式，影响页面的性能。并且css表达式只被IE支持。

<hr>

- **将 CSS 和 JS 放到外部文件中**

　　目的是缓存文件，可以参考原则 4。 但有时候为了减少请求，也会直接写到页面里，需根据 PV 和 IP 的比例权衡。

<hr>

- **权衡 DNS 查找次数**

　　减少主机名可以节省响应时间。但同时，需要注意，减少主机会减少页面中并行下载的数量。

　　IE浏览器在同一时刻只能从同一域名下载两个文件。当在一个页面显示多张图片时，IE 用户的图片下载速度就会受到影响。所以新浪会搞N个二级域名来放图片。

　　下面是新浪微博的图片域名，我们可以看到他有多个域名，这样可以保证这些不同域名能够同时去下载图片，而不用排队。不过如果当使用的域名过多时，响应时间就会慢，因为不用响应域名时间不一致。

<img src="{{ '/styles/images/web/optimize_2.png' | prepend: site.baseurl }}" alt="Expires 优化" />

<hr>

- **精简 CSS 和 JS**

　　这里就涉及到 css 和 js 的压缩了。比如下面的新浪的一个 css 文件，把空格回车全部去掉，减少文件的大小。现在的压缩工具有很多，基本主流的前端构建工具都能进行 css 和 js 文件的压缩，如 grunt，glup 等。

<hr>

- **避免跳转**

　　有种现象会比较坑爹，看起来没什么差别，其实多次了一次页面跳转。比如当URL本该有斜杠（/）却被忽略掉时。例如，当我们要访问 _http://baidu.com_ 时，实际上返回的是一个包含301代码的跳转，它指向的是 _http://baidu.com/_（注意末尾的斜杠）。在 nginx 服务器可以使用 rewrite；Apache 服务器中可以使用 Alias 或者 mod_rewrite 或者 the DirectorySlash 来避免。

　　另一种是不用域名之间的跳转， 比如访问  _http://baidu.com/bbs_ 跳转到　_http://bbs.baidu.com/_。那么可以通过使用 Alias 或者 mod_rewirte 建立 CNAME（保存一个域名和另外一个域名之间关系的 DNS 记录）来替代。

<hr>

- **删除重复的 JS 和 CSS**

　　重复调用脚本，除了增加额外的HTTP请求外，多次运算也会浪费时间。在 IE 和 Firefox 中不管脚本是否可缓存，它们都存在重复运算`JavaScript`的问题。

<hr>

- **配置 ETags**

　　它用来判断浏览器缓存里的元素是否和原来服务器上的一致。比 last-modified date 更具有弹性，例如某个文件在1秒内修改了10次，Etag 可以综合 Inode (文件的索引节点(inode)数)，MTime (修改时间)和 Size 来精准的进行判断，避开 UNIX 记录 MTime 只能精确到秒的问题。 服务器集群使用，可取后两个参数。使用 ETags 减少 Web 应用带宽和负载。

<hr>

- **可缓存的 AJAX**

　　异步请求同样的造成用户等待，所以使用 ajax 请求时，要主动告诉浏览器如果该请求有缓存就去请求缓存内容。如下代码片段, cache:true 就是显式的要求如果当前请求有缓存的话，直接使用缓存：

```js
 $.ajax({
     url : 'url',
     dataType : "json",
     cache: true,
     success : function(son, status){ 
       // do something;
     })
```

<hr>

- **使用 GET 来完成 AJAX 请求**

　　当使用 XMLHttpRequest 时，浏览器中的 POST 方法是一个“两步走”的过程：首先发送文件头，然后才发送数据。因此使用 GET 获取数据时更加有意义。

<hr>

- **减少 DOM 元素数量**

　　这是一门大学问，这里可以引申出一堆优化的细节。想要具体研究的可以看后面推荐书籍。总之大原则减少 DOM 数量，就会减少浏览器的解析负担。

<hr>

- **避免404**

　　比如外链的 css 或者 js 文件出现问题返回 404 时，会破坏浏览器对文件的并行加载。并且浏览器会把试图在返回的 404 响应内容中找到可能有用的部分当作 JavaScript 代码来执行。
    
<hr>

- **减少 Cookie 的大小**

　　Cookie 里面别塞那么多东西，因为每个请求都得带着他跑。

<hr>

- **使用无 cookie 的域**

　　比如 CSS、js、图片等，客户端请求静态文件的时候，减少了 Cookie 的反复传输对主域名的影响。

<hr>

- **不要使用滤镜**

　　IE 独有属性 AlphaImageLoader 用于修正7.0以下版本中显示PNG图片的半透明效果。这个滤镜的问题在于浏览器加载图片时它会终止内容的呈现并且冻结浏览器。在每一个元素（不仅仅是图片）它都会运算一次，增加了内存开支，因此它的问题是多方面的。完全避免使用 AlphaImageLoader 的最好方法就是使用PNG8格式来代替，这种格式能在IE中很好地工作。如果你确实需要使用 AlphaImageLoader，请使用下划线 _filter 又使之对 IE7 以上版本的用户无效。

<hr>

- **不要在 HTML 中缩放图片**

　　比如你需要的图片尺寸是50* 50

```html
<img width=”50″ height=”50″ src=“hahah.jpg” alt=”hahaha” />
```

　　那就不用用一张500*500的大尺寸图片，影响加载。

<hr>

- **缩小 favicon.ico ，并缓存**

　　很多时候开发者都会忽略这个文件，但是每当有用户收藏网站/网页时，浏览器会自动请求这个文件，就算这个图标文件没有在你的网页中明显说明，浏览器也会请求，如果不添加就会出现 404。


<p class="tc">-------------- 分割线 --------------</p>

　　在文章开始就已经提到过了，前端优化是一个很大的话题，远远不是这23条就可以说完了，下面一些是个人实践或参考各方大牛的实践总结出来的（一部分常用）优化技巧。

## HTML 性能优化

<hr>

1. HTML标签有始终。 减少浏览器的判断时间

2. 减少`iframe`的使用，因为`iframe`会增加一条`http`请求，阻止页面加载，即使内容为空，加载也需要时间

3. id和class，在能看明白的基础上，简化命名，在含有关键字的连接词中连接符号用'-'，不要用'_'（未测试）

4. 保持统一大小写，统一大小写有利于浏览器缓存，虽然浏览器不区分大小写，但是 w3c 标准为小写

5. 清除空格，虽然空格有助于我们查看代码，但是每个空格相当于一个字符，空格越多，页面体积越大，像 google、baidu 等搜索引擎的首页去掉了所有可以去掉的空格、回车等字符，这样可以加快 web 页面的传输。

6. 减少不必要的嵌套，尽量扁平化，因为当浏览器编译器遇到一个标签时就开始寻找它的结束标签，直到它匹配上才能显示它的内容，所以当嵌套很多时打开页面就会特别慢。

7. 减少注释，因为过多注释不光占用空间，如果里面有大量关键词会影响搜索引擎的搜索

8. 代码要结构化、语义化

9. 除去无用的标签和空标签

10. 尽量少使用废弃的标签，如b、i等，尽管高版本浏览器是向后兼容的

## CSS 性能优化

<hr>

1. 慎重使用高性能属性：浮动、定位；

2. 尽量减少页面重排、重绘   重排按照css的书写顺序：

```
位置：positon、top、left、z-index、float、dispay   

大小：width、height、margin、padding   

文字系列： font、line-height、color、letter-spacing   

背景边框：background、 border   

其它：anmation、transition   

重绘:border、outline、background、box-shadow,能使用background-color,就尽量不要使用background;
```

3. 去除空规则：｛｝；

4. 属性值为0时，不加单位；

5. 属性值为浮动小数0.**，可以省略小数点之前的0；

6. 标准化各种浏览器前缀：带浏览器前缀的在前。标准属性在后；

7. 不使用@import前缀，它会影响css的加载速度；

8. 充分利用css继承属性，减少代码量；

9. 抽象提取公共样式，减少代码量；

10. 选择器优化嵌套，尽量避免层级过深；

11. css雪碧图，同一页面相近部分的小图标，方便使用，减少页面的请求次数，但是同时图片本身会变大，使用时，优劣考虑清楚，再使用;

## 图片优化

<hr>

- **使用 base64 编码代替图片**   

　　适用于图片小于2KB，页面引用图片不多的情况。将图片转换为base64编码字符串inline到CSS或页面中，减少http的请求次数。

- **合并图片sprite（雪碧图）**   

　　任何用到页面图片的场景。将多个页面用到的北京图片合并成一个大的图片在页面中引用，可以有效地减少请求个数。

- **使用canvas代替图片**  

　　需要高性能的图片或动画，使用HTML5的canvas元素绘制图片，页面渲染性能较高。

- **响应式图片**   

　　不同终端对同一图片的需求不一样，根据终端加载不同的图片来节省不必要的流量。通过picture元素，picturefill或平台判断来为不同终端平台输出不同的图片。减少没必要的图片加载，灵活控制。

- **图片压缩**   

　　在不得不加载图片的前提下，进一步提高优化效果，通过有损或无损压缩所见图片的大小。减少图片加载流量，效果明显。

- **更好的图片格式**  
 
　　webp、bpg、sharpP等新图片格式具有更好的压缩比

## JavaScript 性能优化

<hr>

> **常见的 DOM 操作的优化方法**

- 最小化dom访问次数，尽可能在js端执行；
- 如果需要多次访问某个dom节点，请使用局部变量存储对它的引用；
- 小心处理html集合，因为它实时连系着底层的文档，把集合的长度缓存到一个变量中，并在迭代中使用它，如果需要经常操作集合，建议把它拷贝到一个数组中；
- 如果可能的话，使用速度更快的API，比如querySelectorAll和firstElementChild；
- 要留意重绘和重排；批量修改样式时，“离线”操作dom树；使用缓存，并减少访问布局的次数；
- 动画中使用绝对定位，使用拖放代理
- 使用事件委托来减少事件处理器的数量

> **对数据访问的优化**

- 函数中读写局部变量总是最快的，而全局变量的读取则是最慢的；
- 尽可能地少用with 语句，因为它会增加with 语句以外的数据的访问代价；
- 闭包尽管强大，但不可滥用，否则会影响到执行速度以及内存；
- 嵌套的对象成员会明显影响性能，尽量少用；
- 避免多次访问对象成员或函数中的全局变量，尽量将它们赋值给局部变量以缓存。

> **实践小技巧**

- 字符串判断使用正则表达式
- 数字转换成字符：("" +) > String() > .toString() > new String()
- 数组拼接字符串用join(),不要用+=。
- 浮点数转换成整型：使用Math.floor()或者Math.round()。而不是使用parseInt(),该方法用于将字符串转换成数字。
- 动画中使用绝对定位，使用拖放代理
- 使用事件委托来减少事件处理器的数量
- switch 替代 if else
- var a,b,c; 替代 var a;var b; var c;
- xxx[i++]; 替代  xxx[i];i++;
- i++; 替代  i=i+1;
- i+=5; 替代 i=i+5;

<hr>

<div class="text-center"><h3>移动 H5 性能优化指南</h3><img src="{{ '/styles/images/web/optimize_3.jpg' | prepend: site.baseurl }}" alt="H5 优化" />
</div>