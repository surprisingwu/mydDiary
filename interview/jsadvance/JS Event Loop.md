# JS Event Loop
> 节选自[阮一峰]([JavaScript 运行机制详解：再谈Event Loop - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2014/10/event-loop.html))的博客  
#### 为什么JavaScript是单线程
`JavaScript`语言的一大特点就是单线程，也就是说，同一个时间只能做一件事。那么，为什么`JavaScript`不能有多个线程呢？这样能提高效率啊。

`JavaScript`的单线程，与它的用途有关。作为浏览器脚本语言，`JavaScript`的主要用途是与用户互动，以及操作`DOM`。这决定了它只能是单线程，否则会带来很复杂的同步问题。比如，假定`JavaScript`同时有两个线程，一个线程在某个`DOM`节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

所以，为了避免复杂性，从一诞生，`JavaScript`就是单线程，这已经成了这门语言的核心特征，将来也不会改变。

为了利用多核`CPU`的计算能力，`HTML5提出Web Worker标准`，允许`JavaScript`脚本创建多个线程，但是子线程完全受主线程控制，且不得操作`DOM`。所以，这个新标准并没有改变`JavaScript`单线程的本质。

#### 任务队列
单线程就意味着，所有任务需要排队，前一个任务结束，才会执行后一个任务。如果前一个任务耗时很长，后一个任务就不得不一直等着。

如果排队是因为计算量大，`CPU`忙不过来，倒也算了，但是很多时候CPU是闲着的，因为`IO`设备（输入输出设备）很慢（比如`Ajax`操作从网络读取数据），不得不等着结果出来，再往下执行。

`JavaScript`语言的设计者意识到，这时主线程完全可以不管IO设备，挂起处于等待中的任务，先运行排在后面的任务。等到`IO`设备返回了结果，再回过头，把挂起的任务继续执行下去。

于是，所有任务可以分成两种，一种是同步任务`（synchronous）`，另一种是异步任务`（asynchronous`）。同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；异步任务指的是，不进入主线程、而进入"任务队列”（`task queue`）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

具体来说，异步执行的运行机制如下。（同步执行也是如此，因为它可以被视为没有异步任务的异步执行。）

（1）所有同步任务都在主线程上执行，形成一个执行栈（`execution context stack）`。

（2）主线程之外，还存在一个"任务队列”`（task queue）`。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。

（3）一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。

（4）主线程不断重复上面的第三步。

下图就是主线程和任务队列的示意图。
![主线程和任务队列](http://www.ruanyifeng.com/blogimg/asset/2014/bg2014100801.jpg)
只要主线程空了，就会去读取"任务队列"，这就是JavaScript的运行机制。这个过程会不断重复。

#### 事件和回调函数
"任务队列"是一个事件的队列（也可以理解成消息的队列），`IO`设备完成一项任务，就在"任务队列"中添加一个事件，表示相关的异步任务可以进入"执行栈"了。主线程读取"任务队列"，就是读取里面有哪些事件。

"任务队列"中的事件，除了`IO`设备的事件以外，还包括一些用户产生的事件（比如鼠标点击、页面滚动等等）。只要指定过回调函数，这些事件发生时就会进入"任务队列"，等待主线程读取。

所谓"回调函数”`（callback）`，就是那些会被主线程挂起来的代码。异步任务必须指定回调函数，当主线程开始执行异步任务，就是执行对应的回调函数。

"任务队列"是一个先进先出的数据结构，排在前面的事件，优先被主线程读取。主线程的读取过程基本上是自动的，只要执行栈一清空，"任务队列"上第一位的事件就自动进入主线程。但是，由于存在后文提到的"定时器"功能，主线程首先要检查一下执行时间，某些事件只有到了规定的时间，才能返回主线程。

#### Event Loop
主线程从"任务队列"中读取事件，这个过程是循环不断的，所以整个的这种运行机制又称为`Event Loop`（事件循环）。

为了更好地理解Event Loop，请看下图（转引自Philip Roberts的演讲《Help, I'm stuck in an event-loop》）。
![event loop](http://www.ruanyifeng.com/blogimg/asset/2014/bg2014100802.png)


上图中，主线程运行的时候，产生堆`（heap）和栈（stack`），栈中的代码调用各种外部API，它们在"任务队列"中加入各种事件`（click，load，done）`。只要栈中的代码执行完毕，主线程就会去读取"任务队列"，依次执行那些事件所对应的回调函数。

执行栈中的代码（同步任务），总是在读取"任务队列"（异步任务）之前执行。请看下面这个例子。
```JavaScript

    var req = new XMLHttpRequest();
    req.open('GET', url);    
    req.onload = function (){};    
    req.onerror = function (){};    
    req.send();
```
上面代码中的`req.send`方法是Ajax操作向服务器发送数据，它是一个异步任务，意味着只有当前脚本的所有代码执行完，系统才会去读取"任务队列"。所以，它与下面的写法等价。

```JavaScript
    var req = new XMLHttpRequest();
    req.open('GET', url);
    req.send();
    req.onload = function (){};    
    req.onerror = function (){};   
```
也就是说，指定回调函数的部分（`onload和onerror`），在`send()`方法的前面或后面无关紧要，因为它们属于执行栈的一部分，系统总是执行完它们，才会去读取"任务队列"。

#### 定时器
除了放置异步任务的事件，"任务队列"还可以放置定时事件，即指定某些代码在多少时间之后执行。这叫做"定时器”（`timer`）功能，也就是定时执行的代码。
定时器功能主要由`setTimeout()和setInterval()`这两个函数来完成，它们的内部运行机制完全一样，区别在于前者指定的代码是一次性执行，后者则为反复执行。以下主要讨论`setTimeout()`。

`setTimeout()`接受两个参数，第一个是回调函数，第二个是推迟执行的毫秒数。
```JavaScript
console.log(1);
setTimeout(function(){console.log(2);},1000);
console.log(3);
```
上面代码的执行结果是1，3，2，因为`setTimeout()`将第二行推迟到1000毫秒之后执行。

如果将`setTimeout()`的第二个参数设为0，就表示当前代码执行完（执行栈清空）以后，立即执行（0毫秒间隔）指定的回调函数。

```JavaScript
setTimeout(function(){console.log(1);}, 0);
console.log(2);
```
上面代码的执行结果总是2，1，因为只有在执行完第二行以后，系统才会去执行"任务队列"中的回调函数。

总之，`setTimeout(fn,0)`的含义是，指定某个任务在主线程最早可得的空闲时间执行，也就是说，尽可能早得执行。它在"任务队列"的尾部添加一个事件，因此要等到同步任务和"任务队列"现有的事件都处理完，才会得到执行。

`HTML5`标准规定了`setTimeout()`的第二个参数的最小值（最短间隔），不得低于4毫秒，如果低于这个值，就会自动增加。在此之前，老版本的浏览器都将最短间隔设为10毫秒。另外，对于那些DOM的变动（尤其是涉及页面重新渲染的部分），通常不会立即执行，而是每16毫秒执行一次。这时使用`requestAnimationFrame()`的效果要好于`setTimeout()`。

需要注意的是，`setTimeout()`只是将事件插入了"任务队列"，必须等到当前代码（执行栈）执行完，主线程才会去执行它指定的回调函数。要是当前代码耗时很长，有可能要等很久，所以并没有办法保证，回调函数一定会在`setTimeout()`指定的时间执行。

#### JS 中的微任务和宏任务
* 宏任务`macrotask`:
	* `script 全部代码`
	* `setTimeout`
	* `setInterval`
	* `setInmediate  nodejs 独有的`
	* `requestAnimationFrame 浏览器 告诉浏览器下次dom重绘执行的动画, 接受一个回调函数`
	* `I/O 请求, 文件操作`
	* `UI rendering ui渲染 浏览器独有`
* 微任务`microtask`: 微任务会追加到当前事件循环(执行栈)的末尾
	* `process.nextTick  nodejs独有`
	* `Promise.then`
	* `Object.observe`
	* `MutationObserver 监听dom树变化执行的回调`

* `nodejs event loop`:[The Node.js Event Loop, Timers, and process.nextTick() | Node.js](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)
*  `js和nodejs`的事件循环机制是不一样的
	* `js`: 是主线程执行完,后清空主线程, 每次从任务队列中取一个事件, 如果是微任务则直接追加到当前事件循环的末尾。 `js`调用栈（主线程）遵循后进先出。 而任务队列则是先进先出
		* 执行的顺序是： `sync>microtash>macrotask`
	* `nodejs`: 执行主线程, 再执行微任务(`process.necttick or promise`), 再分六个阶段依次执行, 每个阶段中间会执行微任务. 执行到每个阶段时, 是执行完当前的任务队列 和`js`不一样.
		* 执行的顺序是: `sync>microtask>macrotask>microtask....` 
#mydiary/interview/jsAdvance