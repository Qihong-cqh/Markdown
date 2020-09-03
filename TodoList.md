### 浏览器相关
####  1.跨域

##### 什么是跨越，协议，地址，端口号

跨域方式：
* jsonp
1. 首先前端需要先设置好回调函数，并将其作为 url 的参数。
2. 服务端接收到请求后，通过该参数获取到回调函数名，并将数据放在参数中将其返回
3. 收到结果后因为是 script 标签，所以浏览器会当做是脚本进行运行，从而达到跨域获取数据的目的
缺点：只支持get方法。无法捕获jsonp请求时的连接异常，只能通过超时进行处理。

* cors
关键是服务器，在于设置相应头中的 Access-Control-Allow-Origin，该值要与请求头中 Origin 一致才能生效，否则将跨域失败。

* 服务器代理


####  2.浏览器缓存-强缓存、协商缓存。缓存位置
强制缓存：浏览器不会像服务器发送任何请求，直接从本地缓存中读取文件并返回Status Code: 200 OK

Expires：过期时间，如果设置了时间，则浏览器会在设置的时间内直接读取缓存，不再请求（1.0）

Cache-Control：当值设为max-age=300时，则代表在这个请求正确返回时间（浏览器也会记录下来）的5分钟内再次加载资源，就会命中强缓存。（1.1）

协商缓存: 向服务器发送请求，服务器会根据这个请求的request header的一些参数来判断是否命中协商缓存，如果命中，则返回304状态码并带上新的response header通知浏览器从缓存中读取资源；

Last-Modifed/If-Modified-Since 和 Etag/If-None-Match是分别成对出现的，呈一一对应关系

https://juejin.im/post/6844903838768431118

####  3.浏览器内核、渲染过程、回流、重绘、

##### 浏览器的渲染过程
 （1）首先解析收到的文档，根据文档定义构建一棵 DOM 树，DOM 树是由 DOM 元素及属性节点组成的。

 （2）然后对 CSS 进行解析，生成 CSSOM 规则树。

 （3）根据 DOM 树和 CSSOM 规则树构建渲染树。渲染树的节点被称为渲染对象，渲染对象是一个包含有颜色和大小等属性的矩
     形，渲染对象和 DOM 元素相对应，但这种对应关系不是一对一的，不可见的 DOM 元素不会被插入渲染树。还有一些 DOM 
     元素对应几个可见对象，它们一般是一些具有复杂结构的元素，无法用一个矩形来描述。

 （4）当渲染对象被创建并添加到树中，它们并没有位置和大小，所以当浏览器生成渲染树以后，就会根据渲染树来进行布局（也
     可以叫做回流）。这一阶段浏览器要做的事情是要弄清楚各个节点在页面中的确切位置和大小。通常这一行为也被称为“自动
     重排”。

 （5）布局阶段结束后是绘制阶段，遍历渲染树并调用渲染对象的 paint 方法将它们的内容显示在屏幕上，绘制使用 UI 基础组
     件。

  值得注意的是，这个过程是逐步完成的，为了更好的用户体验，渲染引擎将会尽可能早的将内容呈现到屏幕上，并不会等到所有的
  html 都解析完成之后再去构建和布局 render 树。它是解析完一部分内容就显示一部分内容，同时，可能还在通过网络下载其
  余内容。

##### 什么是回流和重绘
重绘: 当渲染树中的一些元素需要更新属性，而这些属性只是影响元素的外观、风格，而不会影响布局的操作，比如 background
       -color，我们将这样的操作称为重绘。
 
 回流：当渲染树中的一部分（或全部）因为元素的规模尺寸、布局、隐藏等改变而需要重新构建的操作，会影响到布局的操作，这样
      的操作我们称为回流。

 常见引起回流属性和方法：

 任何会改变元素几何信息（元素的位置和尺寸大小）的操作，都会触发回流。

 （1）添加或者删除可见的 DOM 元素；
 （2）元素尺寸改变——边距、填充、边框、宽度和高度
 （3）内容变化，比如用户在 input 框中输入文字
 （4）浏览器窗口尺寸改变——resize事件发生时
 （5）计算 offsetWidth 和 offsetHeight 属性
 （6）设置 style 属性的值
 （7）当你修改网页的默认字体时。

 回流必定会发生重绘，重绘不一定会引发回流。回流所需的成本比重绘高的多，改变父节点里的子节点很可能会导致父节点的一系列
 回流。

####  4.浏览器性能优化

前端性能优化主要是为了提高页面的加载速度，优化用户的访问体验。我认为可以从这些方面来进行优化。

 第一个方面是页面的内容方面

 （1）通过文件合并、css 雪碧图、使用 base64 等方式来减少 HTTP 请求数，避免过多的请求造成等待的情况。

 （2）通过 DNS 缓存等机制来减少 DNS 的查询次数。

 （3）通过设置缓存策略，对常用不变的资源进行缓存。

 （4）使用延迟加载的方式，来减少页面首屏加载时需要请求的资源。延迟加载的资源当用户需要访问时，再去请求加载。

 （5）通过用户行为，对某些资源使用预加载的方式，来提高用户需要访问资源时的响应速度。

 第二个方面是服务器方面

 （1）使用 CDN 服务，来提高用户对于资源请求时的响应速度。

 （2）服务器端启用 Gzip、Deflate 等方式对于传输的资源进行压缩，减小文件的体积。

 （3）尽可能减小 cookie 的大小，并且通过将静态资源分配到其他域名下，来避免对静态资源请求时携带不必要的 cookie

 第三个方面是 CSS 和 JavaScript 方面

 （1）把样式表放在页面的 head 标签中，减少页面的首次渲染的时间。

 （2）避免使用 @import 标签。

 （3）尽量把 js 脚本放在页面底部或者使用 defer 或 async 属性，避免脚本的加载和执行阻塞页面的渲染。

 （4）通过对 JavaScript 和 CSS 的文件进行压缩，来减小文件的体积。

####  5.本地存储。cookie、sessionstorage、localstorage、IndexDB

1. SessionStorage， LocalStorage， Cookie 这三者都可以被用来在浏览器端存储数据，而且都是字符串类型的键值对。区别
 在于前两者属于 HTML5 WebStorage，创建它们的目的便于客户端存储数据。而 cookie 是网站为了标示用户身份而储存在用户
 本地终端上的数据（通常经过加密）。cookie 数据始终在同源（协议、主机、端口相同）的 http 请求中携带（即使不需要），会
 在浏览器和服务器间来回传递。
 
 
 存储大小：
   	cookie 数据大小不能超过4 k 。
   	sessionStorage 和 localStorage 虽然也有存储大小的限制，但比 cookie 大得多，可以达到 5M 或更大。

 有期时间：
   	localStorage    存储持久数据，浏览器关闭后数据不丢失除非主动删除数据。
   	sessionStorage  数据在页面会话结束时会被清除。页面会话在浏览器打开期间一直保持，并且重新加载或恢复页面仍会
                     保持原来的页面会话。在新标签或窗口打开一个页面时会在顶级浏览上下文中初始化一个新的会话。
   	cookie          设置的 cookie 过期时间之前一直有效，即使窗口或浏览器关闭。
  
 作用域：
     sessionStorage  只在同源的同窗口（或标签页）中共享数据，也就是只在当前会话中共享。
     localStorage    在所有同源窗口中都是共享的。
     cookie          在所有同源窗口中都是共享的。

2. 浏览器端常用的存储技术是 cookie 、localStorage 和 sessionStorage。

 cookie 其实最开始是服务器端用于记录用户状态的一种方式，由服务器设置，在客户端存储，然后每次发起同源请求时，发送给服
 务器端。cookie 最多能存储 4 k 数据，它的生存时间由 expires 属性指定，并且 cookie 只能被同源的页面访问共享。

 sessionStorage 是 html5 提供的一种浏览器本地存储的方法，它借鉴了服务器端 session 的概念，代表的是一次会话中所保
 存的数据。它一般能够存储 5M 或者更大的数据，它在当前窗口关闭后就失效了，并且 sessionStorage 只能被同一个窗口的同源
 页面所访问共享。

 localStorage 也是 html5 提供的一种浏览器本地存储的方法，它一般也能够存储 5M 或者更大的数据。它和 sessionStorage 
 不同的是，除非手动删除它，否则它不会失效，并且 localStorage 也只能被同源页面所访问共享。

 上面几种方式都是存储少量数据的时候的存储方式，当我们需要在本地存储大量数据的时候，我们可以使用浏览器的 indexDB 这是浏
 览器提供的一种本地的数据库存储机制。它不是关系型数据库，它内部采用对象仓库的形式存储数据，它更接近 NoSQL 数据库。

3. 我的理解是 cookie 是服务器提供的一种用于维护会话状态信息的数据，通过服务器发送到浏览器，浏览器保存在本地，当下一次有同源的请求时，将保存的 cookie 值添加到请求头部，发送给服务端。这可以用来实现记录用户登录状态等功能。cookie 一般可以存储 4k 大小的数据，并且只能够被同源的网页所共享访问。

服务器端可以使用 Set-Cookie 的响应头部来配置 cookie 信息。一条cookie 包括了5个属性值 expires、domain、path、secure、HttpOnly。其中 expires 指定了 cookie 失效的时间，domain 是域名、path是路径，domain 和 path 一起限制了 cookie 能够被哪些 url 访问。secure 规定了 cookie 只能在确保安全的情况下传输，HttpOnly 规定了这个 cookie 只能被服务器访问，不能使用 js 脚本访问。

在发生 xhr 的跨域请求的时候，即使是同源下的 cookie，也不会被自动添加到请求头部，除非显示地规定。

#### 浏览器的同源政策
我对浏览器的同源政策的理解是，一个域下的 js 脚本在未经允许的情况下，不能够访问另一个域的内容。这里的同源的指的是两个
域的协议、域名、端口号必须相同，否则则不属于同一个域。

同源政策主要限制了三个方面

第一个是当前域下的 js 脚本不能够访问其他域下的 cookie、localStorage 和 indexDB。

第二个是当前域下的 js 脚本不能够操作访问操作其他域下的 DOM。

第三个是当前域下 ajax 无法发送跨域请求。

同源政策的目的主要是为了保证用户的信息安全，它只是对 js 脚本的一种限制，并不是对浏览器的限制，对于一般的 img、或者
script 脚本请求都不会有跨域的限制，这是因为这些操作都不会通过响应结果来进行可能出现安全问题的操作。

####  短轮询、长轮询、websocket、webworker

### js相关

#### 1. 事件处理/事件委托

##### 事件流：
1. 冒泡
2. 捕获
3. DOM 事件流：先捕获、在冒泡
   

事件是用户操作网页时发生的交互动作或者网页本身的一些操作，现代浏览器一共有三种事件模型。

0. html事件（onclick）

1. 第一种事件模型是最早的 DOM0 级模型，这种模型不会传播，所以没有事件流的概念，但是现在有的浏览器支持以冒泡的方式实
现，它可以在网页中直接定义监听函数，也可以通过 js 属性来指定监听函数。这种方式是所有浏览器都兼容的。（getElementById，XX.onclick）

2. 第二种事件模型是 IE 事件模型，在该事件模型中，一次事件共有两个过程，事件处理阶段，和事件冒泡阶段。事件处理阶段会首先执行目标元素绑定的监听事件。然后是事件冒泡阶段，冒泡指的是事件从目标元素冒泡到 document，依次检查经过的节点是否绑定了事件监听函数，如果有则执行。这种模型通过 attachEvent 来添加监听函数，可以添加多个监听函数，会按顺序依次执行。（attachEvent(事件名、处理函数)、detachEvent(事件名、处理函数)---均为冒泡阶段）

3. 第三种是 DOM2 级事件模型，在该事件模型中，一次事件共有三个过程，第一个过程是事件捕获阶段。捕获指的是事件从 document 一直向下传播到目标元素，依次检查经过的节点是否绑定了事件监听函数，如果有则执行。后面两个阶段和 IE 事件模型的两个阶段相同。这种事件模型，事件绑定的函数是 addEventListener，其中第三个参数可以指定事件是否在捕获阶段执行。(addEventListener(事件名,处理函数,bool)、removeEventListener(事件名、处理函数、bool)、true 表示在捕获阶段调用，false 表示在事件冒泡阶段调用)

```
//跨浏览器事件处理程序

var EventUtil={
    addHandler:function(element,type,handler){
        if(element.addEventListener){
            element.addEventListener(type,handler,false)
        }else if(element.attachEvent){
            element.attachEvent("on"+type,handler)
        }else {
            element["on"+type]=handler;
        }
    },
    removeHandler:function(element,type,handler){
        if(element.removeEventListener){
            element.removeEventListener(type,handler,false)
        }else if(element.detachEvent){
            element.detachEvent("on"+type,handler)
        }else{
            element["on"+type]=null
        }
    }
}
```

#### 2. 事件循环--eventloop

1. 因为 js 是单线程运行的，在代码执行的时候，通过将不同函数的执行上下文压入执行栈中来保证代码的有序执行。在执行同步代码的时候，如果遇到了异步事件，js 引擎并不会一直等待其返回结果，而是会将这个事件挂起，继续执行执行栈中的其他任务。当异步事件执行完毕后，再将异步事件对应的回调加入到与当前执行栈中不同的另一个任务队列中等待执行。

任务队列可以分为宏任务对列和微任务对列，当当前执行栈中的事件执行完毕后，js 引擎首先会判断微任务对列中是否有任务可以执行，如果有就将微任务队首的事件压入栈中执行。当微任务对列中的任务都执行完成后再去判断宏任务对列中的任务。

微任务包括了 promise 的回调、node 中的 process.nextTick 、对 Dom 变化监听的 MutationObserver。

宏任务包括了 script 脚本的执行、setTimeout ，setInterval ，setImmediate 一类的定时事件，还有如 I/O 操作、UI 渲染等。


同步和异步任务分别进入不同的执行"场所"，同步的进入主线程，异步的进入Event Table并注册函数。
当指定的事情完成时，Event Table会将这个函数移入Event Queue。
主线程内的任务执行完毕为空，会去Event Queue读取对应的函数，进入主线程执行。
上述过程会不断重复，也就是常说的Event Loop(事件循环)。

#### 3. es6 增强语法、模块化
#### 4. 异步流程：promise、settimeout、settimeinterval、await、async
#### 5.  手写代码
#### 6.  相关api:数组、字符串、对象、正则、dom、bom


####  垃圾回收 ： v8 javascript
###### v8
v8 的垃圾回收机制基于分代回收机制，这个机制又基于世代假说，这个假说有两个特点，一是新生的对象容易早死，另一个是不死的对象会活得更久。基于这个假说，v8 引擎将内存分为了新生代和老生代。

新创建的对象或者只经历过一次的垃圾回收的对象被称为新生代。经历过多次垃圾回收的对象被称为老生代。

新生代被分为 From 和 To 两个空间，To 一般是闲置的。当 From 空间满了的时候会执行 Scavenge 算法进行垃圾回收。当我们执行垃圾回收算法的时候应用逻辑将会停止，等垃圾回收结束后再继续执行。这个算法分为三步：

（1）首先检查 From 空间的存活对象，如果对象存活则判断对象是否满足晋升到老生代的条件，如果满足条件则晋升到老生代。如果不满足条件则移动 To 空间。

（2）如果对象不存活，则释放对象的空间。

（3）最后将 From 空间和 To 空间角色进行交换。

新生代对象晋升到老生代有两个条件：

（1）第一个是判断是对象否已经经过一次 Scavenge 回收。若经历过，则将对象从 From 空间复制到老生代中；若没有经历，则复制到 To 空间。

（2）第二个是 To 空间的内存使用占比是否超过限制。当对象从 From 空间复制到 To 空间时，若 To 空间使用超过 25%，则对象直接晋升到老生代中。设置 25% 的原因主要是因为算法结束后，两个空间结束后会交换位置，如果 To 空间的内存太小，会影响后续的内存分配。

老生代采用了标记清除法和标记压缩法。标记清除法首先会对内存中存活的对象进行标记，标记结束后清除掉那些没有标记的对象。由于标记清除后会造成很多的内存碎片，不便于后面的内存分配。所以了解决内存碎片的问题引入了标记压缩法。

由于在进行垃圾回收的时候会暂停应用的逻辑，对于新生代方法由于内存小，每次停顿的时间不会太长，但对于老生代来说每次垃圾回收的时间长，停顿会造成很大的影响。 为了解决这个问题 V8 引入了增量标记的方法，将一次停顿进行的过程分为了多步，每次执行完一小步就让运行逻辑执行一会，就这样交替运行。

###### javascript



### 算法

#### 1. 基本排序算法
#### 2. 数据结构：栈、队列、链表、二叉树、图、并查集（set、map）
#### 3. 算法：dfs、bfs
#### 4. 算法思想：动态规划、分治、回溯、dfs

### vue
#### 1. 生命周期
Vue 一共有8个生命阶段，分别是创建前、创建后、加载前、加载后、更新前、更新后、销毁前和销毁后，每个阶段对应了一个生命周期的钩子函数。

（1）beforeCreate 钩子函数，在实例初始化之后，在数据监听和事件配置之前触发。因此在这个事件中我们是获取不到 data 数据的。

（2）created 钩子函数，在实例创建完成后触发，此时可以访问 data、methods 等属性。但这个时候组件还没有被挂载到页面中去，所以这个时候访问不到 $el 属性。一般我们可以在这个函数中进行一些页面初始化的工作，比如通过 ajax 请求数据来对页面进行初始化。

（3）beforeMount 钩子函数，在组件被挂载到页面之前触发。在 beforeMount 之前，会找到对应的 template，并编译成 render 函数。

（4）mounted 钩子函数，在组件挂载到页面之后触发。此时可以通过 DOM API 获取到页面中的 DOM 元素。

（5）beforeUpdate 钩子函数，在响应式数据更新时触发，发生在虚拟 DOM 重新渲染和打补丁之前，这个时候我们可以对可能会被移除的元素做一些操作，比如移除事件监听器。

（6）updated 钩子函数，虚拟 DOM 重新渲染和打补丁之后调用。

（7）beforeDestroy 钩子函数，在实例销毁之前调用。一般在这一步我们可以销毁定时器、解绑全局事件等。

（8）destroyed 钩子函数，在实例销毁之后调用，调用后，Vue 实例中的所有东西都会解除绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。

当我们使用 keep-alive 的时候，还有两个钩子函数，分别是 activated 和 deactivated 。用 keep-alive 包裹的组件在切换时不会进行销毁，而是缓存到内存中并执行 deactivated 钩子函数，命中缓存渲染后会执行 actived 钩子函数。

#### 2. 双向绑定原理
利用了 Object.defineProperty() 这个方法重新定义了对象获取属性值(get)和设置属性值(set)。

vue 通过使用双向数据绑定，来实现了 View 和 Model 的同步更新。vue 的双向数据绑定主要是通过使用数据劫持和发布订阅者模式来实现的。

首先我们通过 Object.defineProperty() 方法来对 Model 数据各个属性添加访问器属性，以此来实现数据的劫持，因此当 Model 中的数据发生变化的时候，我们可以通过配置的 setter 和 getter 方法来实现对 View 层数据更新的通知。

数据在 html 模板中一共有两种绑定情况，一种是使用 v-model 来对 value 值进行绑定，一种是作为文本绑定，在对模板引擎进行解析的过程中。

如果遇到元素节点，并且属性值包含 v-model 的话，我们就从 Model 中去获取 v-model 所对应的属性的值，并赋值给元素的 value 值。然后给这个元素设置一个监听事件，当 View 中元素的数据发生变化的时候触发该事件，通知 Model 中的对应的属性的值进行更新。

如果遇到了绑定的文本节点，我们使用 Model 中对应的属性的值来替换这个文本。对于文本节点的更新，我们使用了发布订阅者模式，属性作为一个主题，我们为这个节点设置一个订阅者对象，将这个订阅者对象加入这个属性主题的订阅者列表中。当 Model 层数据发生改变的时候，Model 作为发布者向主题发出通知，主题收到通知再向它的所有订阅者推送，订阅者收到通知后更改自己的数
据。

有一些对属性的操作，使用这种方法无法拦截，比如说通过下标方式修改数组数据或者给对象新增属性，vue 内部通过重写函数解决了这个问题。在 Vue3.0 中已经不使用这种方式了，而是通过使用 Proxy 对对象进行代理，从而实现数据劫持。使用 Proxy 的好处是它可以完美的监听到任何方式的数据改变，唯一的缺点是兼容性的问题，因为这是 ES6 的语法。

#### 3. vue组件的通信
（1）父子组件间通信

第一种方法是子组件通过 props 属性来接受父组件的数据，然后父组件在子组件上注册监听事件，子组件通过 emit 触发事
件来向父组件发送数据。

第二种是通过 ref 属性给子组件设置一个名字。父组件通过 $refs 组件名来获得子组件，子组件通过 $parent 获得父组
件，这样也可以实现通信。

第三种是使用 provider/inject，在父组件中通过 provider 提供变量，在子组件中通过 inject 来将变量注入到组件
中。不论子组件有多深，只要调用了 inject 那么就可以注入 provider 中的数据。

（2）兄弟组件间通信

第一种是使用 eventBus 的方法，它的本质是通过创建一个空的 Vue 实例来作为消息传递的对象，通信的组件引入这个实
例，通信的组件通过在这个实例上监听和触发事件，来实现消息的传递。

第二种是通过 $parent.$refs 来获取到兄弟组件，也可以进行通信。

（3）任意组件之间

使用 eventBus ，其实就是创建一个事件中心，相当于中转站，可以用它来传递事件和接收事件。


如果业务逻辑复杂，很多组件之间需要同时处理一些公共的数据，这个时候采用上面这一些方法可能不利于项目的维护。这个时候
可以使用 vuex ，vuex 的思想就是将这一些公共的数据抽离出来，将它作为一个全局的变量来管理，然后其他组件就可以对这个
公共数据进行读写操作，这样达到了解耦的目的。

#### 4. 前端路由的实现。vue-router
#### 5. vuex 状态管理
#### 6. computed & watch
computed 是计算属性，依赖其他属性计算值，并且 computed 的值有缓存，只有当计算值变化才会返回内容。

watch 监听到值的变化就会执行回调，在回调中可以进行一些逻辑操作。
（1）computed 是计算一个新的属性，并将该属性挂载到 Vue 实例上，而 watch 是监听已经存在且已挂载到 Vue 实例上的数据，所以用 watch 同样可以监听 computed 计算属性的变化。

（2）computed 本质是一个惰性求值的观察者，具有缓存性，只有当依赖变化后，第一次访问 computed 属性，才会计算新的值。而 watch 则是当数据发生变化便会调用执行函数。

（3）从使用场景上说，computed 适用一个数据被多个数据影响，而 watch 适用一个数据影响多个数据。

### 操作系统
#### 1. 进程&线程

1.进程，是并发执行的程序在执行过程中分配和管理资源的基本单位，是一个动态概念，竞争计算机系统资源的基本单位。


2.线程，是进程的一部分，一个没有线程的进程可以被看作是单线程的。线程有时又被称为轻权进程或轻量级进程，也是 CPU 调度的一个基本单位。


一个程序至少有一个进程，一个进程至少有一个线程，资源分配给进程，同一个进程下所有线程共享该进程的资源


#### 2. 那些资源共享、那些不共享
1.共享的资源有

1.堆：由于堆是在进程空间中开辟出来的，所以它是理所当然地被共享的；因此new出来的都是共享的（16位平台上分全局堆和局部堆，局部堆是独享的）
2.全局变量：它是与具体某一函数无关的，所以也与特定线程无关；因此也是共享的
3.静态变量：虽然对于局部变量来说，它在代码中是“放”在某一函数中的，但是其存放位置和全局变量一样，存于堆中开辟的.bss和.data段，是共享的
4.文件等公用资源：这个是共享的，使用这些公共资源的线程必须同步。Win32 提供了几种同步资源的方式，包括信号、临界区、事件和互斥体。


2.独享的资源有

1.栈：栈是独享的
2.寄存器：这个可能会误解，因为电脑的寄存器是物理的，每个线程去取值难道不一样吗？其实线程里存放的是副本，包括程序计数器PC


#### 3. 通信方式

1.无名管道：半双工的通信方式，数据只能单向流动且只能在具有亲缘关系的进程间使用
2.高级管道：将另一个程序当作一个新的进程在当前程序进程中启动，则这个进程算是当前程序的子进程，
3.有名管道，：也是半双工的通信方式，但是允许没有亲缘进程之间的通信
4.消息队列：消息队列是有消息的链表，存放在内核中，并由消息队列标识符标识，消息队列克服了信号传递信息少，管道只能承载无格式字节流以及缓冲区大小受限的缺点
5.信号量：信号量是一个计数器，可以用来控制多个进程对共享资源的访问，它常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源，
6.信号：用于通知接受进程某个事件已经发生
7.共享内存：共享内存就是映射一段能被其他进程所访问的内存。这段共享内存由一个进程创建，但是多个进程可以访问，共享内存是最快的IPC 方式，往往与其他通信机制配合使用
8.套接字：可用于不同机器之间的进程通信

### 编译原理
babel的实现？AST

### 疑难杂症！！！
#### 1. 动态原型模式？为什么只能用一次？？？不能用字面量覆盖
//https://github.com/mqyqingfeng/Blog/issues/15
*** 高级程序设计解决：调用构造函数时会为实例添加一个指向最初原型的prototype指针，而把原型修改为另一个对象就切断了构造函数和最初原型之间的联系。

#### 2. new 做了什么！！！
#### 3. 模拟call、bind、apply
#### 4. 柯里化
#### let const var
1. 声明的变量值在声明时代的代码块有效（块级作用域）
2. 不存在变量提升
3. 不允许重复声明，会报错
4. 存在暂时性死区，如果在变量声明前使用会报错

### 手写代码：
#### 实现数组的随机排序（乱序）

```
1. 使用数组sort方法，让 Math.random() 出来的数与 0.5 比较，如果大于就返回 1 交换位置，如果小于就返回 -1，不交换位置。
但是因为sort是依次比较的，所以数组不是完全的随机。

function randomSort(a,b){
    return Math.random()>0.5?-1:1;
}

2. 随机从原数组抽取一个数，加入到新数组
function randomSort(arr){
    var result=[]
    while(arr.length>0){
        var randomIndex=Math.floor(Math.random()*arr.length)
        result.push(arr[randomIndex])
        arr.splice(randomIndex,1)
    }
    return result
}

3. 随机交换数组内的元素
function randomSort(arr){
    var index,randomIndex,temp,len=arr.length;

    for(index=0;index<len;index++){
        randomIndex=Math.floor(Math.random()*(len-index)+index)

        temp=arr[index]
        arr[index]=arr[randomIndex]
        arr[randomIndex]=temp
        //使用es6 语法
        //[arr[index],arr[randomIndex]]=[arr[randomIndex],arr[index]]
    }
    return arr
}
```

#### instanceof

```
function myInstanceof(left,right){
    let proto=Object.getPrototypeOf(left)
    prototype=right.prototype

    while(true){
        if(!proto)  return false
        if(proto===prototype)   return true

        proto=Object.getPrototypeOf(proto)
    }
}
```

#### new 操作符
（1）首先创建了一个新的空对象
（2）设置原型，将对象的原型设置为函数的 prototype 对象。
（3）让函数的 this 指向这个对象，执行构造函数的代码（为这个新对象添加属性）
（4）判断函数的返回值类型，如果是值类型，返回创建的对象。如果是引用类型，就返回这个引用类型的对象。

```
//使用：objectFactory(构造函数, 初始化参数);
function objectFactory(){
    var obj = {};
    //取得该方法的第一个参数(并删除第一个参数)，该参数是构造函数
    var Constructor = [].shift.apply(arguments);
    //将新对象的内部属性__proto__指向构造函数的原型，这样新对象就可以访问原型中的属性和方法
    obj.__proto__ = Constructor.prototype;
    //取得构造函数的返回值
    var ret = Constructor.apply(obj, arguments);
    //如果返回值是一个对象就返回该对象，否则返回构造函数的一个实例对象
    return typeof ret === "object" ? ret : obj;
}
```

#### ajax

1.创建 XMLHttpRequest 对象，也就是创建一个异步调用对象
2.创建一个新的 HTTP 请求，并指定该 HTTP 请求的方法、URL 及验证信息
3.设置响应 HTTP 请求状态变化的函数
4.发送 HTTP 请求
5.获取异步调用返回的数据
6.使用 JavaScript 和 DOM 实现局部刷新

```
//1：创建Ajax对象
var xhr = window.XMLHttpRequest?new XMLHttpRequest():new ActiveXObject('Microsoft.XMLHTTP');// 兼容IE6及以下版本
//2：配置 Ajax请求地址
xhr.open('get','index.xml',true);
//3：发送请求
xhr.send(null); // 严谨写法
//4:监听请求，接受响应
xhr.onreadysatechange=function(){
     if(xhr.readySate==4&&xhr.status==200 || xhr.status==304 )
          console.log(xhr.responsetXML)
}



//------------
const SERVER_URL='/server'
let xhr=new XMLHTTPRequest()

xhr.open('GET',SERVER_URL,true)

xhr.onreadystatechange=function(){
    if(this.readyState !==4 ) return 

    if(this.status === 200){
        handle(this.response)
    }else {
        console.error(this.statusText)
    }
}
xhr.onerror=function(){
    console.error(this.statusText)
}
xhr.responseType='json'
xhr.setRequestHeader('Accept','application/json')

xhr.send(null)
```

#### javascript类型判断函数

```
function getType(value){
    if(value===null){
        return value+""
    }

    if(typeof value === "object"){
        let valueClass = Object.prototype.toString.call(value)
        type=valueClass.split(' ')[1].split('')

        type.pop()

        return type.join('').toLowerCase()
    }else{
        return typeof value
    }
}
```

#### 函数节流和防抖
// 函数防抖： 在事件被触发 n 秒后再执行回调，如果在这 n 秒内事件又被触发，则重新计时。

// 函数节流： 规定一个单位时间，在这个单位时间内，只能有一次触发事件的回调函数执行，如果在同一个单位时间内某事件被触发多次，只有一次能生效。

函数防抖是指在事件被触发 n 秒后再执行回调，如果在这 n 秒内事件又被触发，则重新计时。这可以使用在一些点击请求的事件上，避免因为用户的多次点击向后端发送多次请求。

函数节流是指规定一个单位时间，在这个单位时间内，只能有一次触发事件的回调函数执行，如果在同一个单位时间内某事件被触发多次，只有一次能生效。节流可以使用在 scroll 函数的事件监听上，通过事件节流来降低事件调用的频率。

```
//防抖
function debounce(fn,wait){
    var timer=null

    return function(){
        var context=this,
        args=arguments

        if(timer){
            clearTimeout(timer)
            timer=null
        }

        timer=setTimeout(()=>{
            fn.apply(context,args)
        },wait)
    }
}

//节流
function throttle(fn,delay){
    var preTime=Date.now()
    
    return function(){
        var context=this,
        args=arguments,
        nowTime=Date.now()

        if(nowTime-preTime >= delay){
            preTime=Date.now()
            return fn.apply(context,args)
        }
    }
}
```

#### 深浅拷贝

浅拷贝指的是将一个对象的属性值复制到另一个对象，如果有的属性的值为引用类型的话，那么会将这个引用的地址复制给对象，因此两个对象会有同一个引用类型的引用。浅拷贝可以使用  Object.assign 和展开运算符来实现。

深拷贝相对浅拷贝而言，如果遇到属性值为引用类型的时候，它新建一个引用类型并将对应的值复制给它，因此对象获得的一个新的引用类型而不是一个原有类型的引用。深拷贝对于一些对象可以使用 JSON 的两个函数来实现，但是由于 JSON 的对象格式比 js 的对象格式更加严格，所以如果属性值里边出现函数或者 Symbol 类型的值时，会转换失败。

```
//浅拷贝
function shallowCopy(object){
    if(!object || typeof object !== "object"){
        return
    }
    let newObject = Array.isArray(object)?[]:{}

    for(let key in object){
        if(object.hasOwnProperty(key)){
            newObject[key]=object[key]
        }
    }
    return newObject
}

//深拷贝
function deepCopy(object){
    if(!object || typeof object !== 'object')
        return
    
    let newObject=Array.isArray(object)?[]:{}
    for(let key in object){
        if(object.hasOwnProperty(key)){
            newObject[key] = (typeof object[key] === 'object')?deepCopy(object[key]):object[key]
        }
    }
    return newObject
}
```

#### 手写call、bind、apply

```
//call
1.判断调用对象是否为函数，即使我们是定义在函数的原型上的，但是可能出现使用 call 等方式调用的情况。
2.判断传入上下文对象是否存在，如果不存在，则设置为 window 。
3.处理传入的参数，截取第一个参数后的所有参数。
4.将函数作为上下文对象的一个属性。
5.使用上下文对象来调用这个方法，并保存返回结果。
6.删除刚才新增的属性。
7.返回结果。

Function.prototype.mycall=function(context){
    if(typeof this != 'function'){
        console.log('type error')
    }

    let args=[...arguments].slice(1),
        result=null

    context = context || window

    context.fn=this
    result = context.fn(...args)
    delete context.fn

    return result
}

//apply
1.判断调用对象是否为函数，即使我们是定义在函数的原型上的，但是可能出现使用 call 等方式调用的情况。
2.判断传入上下文对象是否存在，如果不存在，则设置为 window 。
3.将函数作为上下文对象的一个属性。
4.判断参数值是否传入
4.使用上下文对象来调用这个方法，并保存返回结果。
5.删除刚才新增的属性
6.返回结果

Function.prototype.myApply=function(context){
    if(typeof this != 'function'){
        console.log('type error')
    }
    let result=null
    context=context||window

    context.fn=this
    if(argument[1]){
        result=context.fn(...arguments[1])
    }else{
        result=context.fn()
    }
    delete context.fn
    
    return result
}

//bind
1.判断调用对象是否为函数，即使我们是定义在函数的原型上的，但是可能出现使用 call 等方式调用的情况。
2.保存当前函数的引用，获取其余传入参数值。
3.创建一个函数返回
4.函数内部使用 apply 来绑定函数调用，需要判断函数作为构造函数的情况，这个时候需要传入当前函数的 this 给 apply 调用，其余情况都传入指定的上下文对象。

Function.prototype.myBind=function(){
    if(type this!=='function'){
        console.log('type error')
    }

    var args=[...arguments].slice(1),
        fn=this
    
    return function Fn(){
        return fn.apply(
            this instanceof Fn ? this : context,
            args.concat(...arguments)
        )
    }
}
```
参考：<a href='https://github.com/mqyqingfeng/Blog/issues/11'>JavaScript深入之call和apply的模拟实现</a><br>

#### 柯里化 ！！！

```
function curry(fn,...args){
    return fn.length <= args.length ? fn.(...args) : curry.bind(null,fn,...args)
}
```

#### 
#### 
#### 