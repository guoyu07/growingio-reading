growingio-js-sdk阅读代码收获

根据源码中可以理解的语义化的代码百度，
1）mutation-observer
Mutation Observer（变动观察器）是监视DOM变动的接口。当DOM对象树发生任何变动时，Mutation Observer会得到通知。
Observer方法：observer方法指定所要观察的DOM元素，以及要观察的特定变动。
详细信息https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver#Example_usage
MutationObserver所观察的DOM变动（即上面代码的option对象），包含以下类型：
- childList：子元素的变动

- attributes：属性的变动

- characterData：节点内容或节点文本的变动

- subtree：所有下属节点（包括子节点和子节点的子节点）的变动

想要观察哪一种变动类型，就在option对象中指定它的值为true。需要注意的是，不能单独观察subtree变动，必须同时指定childList、attributes和characterData中的一种或多种。

除了变动类型，option对象还可以设定以下属性：
- attributeOldValue：值为true或者为false。如果为true，则表示需要记录变动前的属性值。

- characterDataOldValue：值为true或者为false。如果为true，则表示需要记录变动前的数据值。

- attributesFilter：值为一个数组，表示需要观察的特定属性（比如['class', 'str']）。

disconnect方法和takeRecord方法：
disconnect方法用来停止观察。发生相应变动时，不再调用回调函数。observer.disconnect();
takeRecord方法用来清除变动记录，即不再处理未处理的变动。observer.takeRecord
MutationRecord对象
DOM对象每次发生变化，就会生成一条变动记录。这个变动记录对应一个MutationRecord对象，该对象包含了与变动相关的所有信息。Mutation Observer进行处理的一个个变动对象所组成的数组。
MutationRecord对象包含了DOM的相关信息，有如下属性：
- type:观察的变动类型（attribute、characterData或者childList）。

- target:发生变动的DOM对象。

- addedNodes:新增的DOM对象。

- removeNodes:删除的DOM对象。

- previousSibling:前一个同级的DOM对象，如果没有则返回null。

- nextSibling:下一个同级的DOM对象，如果没有就返回null。

- attributeName:发生变动的属性。如果设置了attributeFilter，则只返回预先指定的属性。

- oldValue:变动前的值。这个属性只对attribute和characterData变动有效，如果发生childList变动，则返回null。

addEventListener()绑定事件的对象方法。
addEventListener()含有三个参数，一个是事件名称，另一个是事件执行的函数，最后一个是事件捕获，
例如：obj.addEventListener("click",function(){},true/false);

Mutation Events   用于监听DOM树结构变化的事件
其中涉及到的事件的方法有
enqueue 事件队列
addListeners 批量增加事件处理
removeListeners 批量移除事件处理
addTransientObserver 添加暂时的观察者
removeTransientObservers 移除暂时的观察者
handleEvent 是ie下调用的事件处理方法

Mutation 事件列表

- DOMAttrModified
- DOMAttributeNameChanged
- DOMCharacterDataModified
- DOMElementNameChanged
- DOMNodeInserted
- DOMNodeRemoved
- DOMNodeInsertedIntoDocument
- DOMSubtreeModified

其中DOMNodeRemoved，DOMNodeInserted 和 DOMSubtreeModified 分别用于 监听元素子项的删除，新增，修改(包括删除和新增），
DOMAttrModified 是监听元素属性的修改，并且能够提供具体的修改动作。
Mutation Events是同步执行的，它的每次调用，都需要从事件队列中取出事件，执行，然后事件队列中溢出，期间需要移动队列元素。

window.WeakMap 对象就是简单的键/值映射.但键只能是对象值,不可以是原始值.
window.setImmediate  该方法用来把一些需要长时间运行的操作放在一个回调函数里,在浏览器完成后面的其他语句后,就立刻执行这个回调函数。

- childList: *true =========================可以观察子元素
- attributes: *true ========================可以观察属性
- characterData: *true ====================可以观察数据
- subtree: *true =========================可以观察所有后代
- attributeOldValue: *true ==================可以保存属性旧值
- characterDataOldValue: ==================*true 可以保存数据旧值
- attributeFilter: ==========================设置指定观察属性元素集合

2）mutation-summary 这个模块是对mutation的一个总结
c函数下的方法
getAttributeOldValue 得到需要记录变动前的属性值
getAttributeNamesMutated  得到需要记录变动前的属性值的集合
characterDataMutated  得到需要记录变动前的数据值
removedFromParent 判断是否从父节点移除
insertedIntoParent 判断是否从父节点插入
getOldParent 得到之前的父节点
f函数下的方法
getChange
getOldParent
getIsReachable
getWasReachable
reachabilityChange
g函数下的方法
getOldParentNode
getOldAttribute
getOldCharacterData
getOldPreviousSibling
w函数下的方法
isMatching
wasMatching
matchabilityChange
parseSelectors
matchesSelector
N函数下的方法
createObserverOptions  是创建Observer的配置文件
validateOptions
createSummaries
checkpointQueryValidators
runQueryValidators
changesToReport
observerCallback
reconnect
takeSummaries
disconnect

3）dom-observer
e的主要方法里面有
registerDomObserver  给注册dom加入观察
sendQueue   send方法的队列
getLeafNodes    得到叶子的节点
nodeMessage               节点消息
registerEventListener   注册事件监听器（主要监控的是click，change，submit）监听到发生变化的数据
visitMessage                访问消息
trackPageView              追踪PV
registerCircleHandler     注册循环处理    引入一个/assets/javascripts/circle-plugin.js这样的一个插件
registerHistoryHandler   通过pushState和replaceState接口操作浏览器历史，并且改变当前页面的URL。
                                   pushState是将指定的URL添加到浏览器历史里，replaceState是将指定的URL替换当前的URL。
pageChanged  页面路径的变化
domLoadedHandler  dom在加载的时候处理的方法是registerDomObserver的一个入口
blind  页面没有监控的时候，要执行的函数部分
observe 是整个观察者的入口在主函数6当中被调用
4）guid 生成动态的id
5）info
6）这个函数是整个growingio的入口
7）messaging-observer
8）shim
函数里面定义了Date.now和String.prototype.trim(去除空格)
9）tracker
主要通过send这个方法用原生的js发送ajax请求到后台的方式来实现跟服务器通信
我发现数据收集完毕之后它是传到http://api.growingio.com这个网址，这个换成我们自己的url能否收到数据？
每个模块的方法下对应的{}花括号内部的内容是其引用模块的内容
e是其收集的主要的数据的内容
发起post请求之后的数据的具体的内容是，也就是收集的大概的数据内容
[{
    "ai": "22222-22222-22222-22222",           //accountId
    "av": "0.40",                                                //是一个写在程序里面的值，估计是版本号
    "b": "Web",                                                 //也是写好的，代表web
    "u": "3a27ab7a-68fd-4c77-92f7-514e334f7fbd",   //userId
    "s": "37e38b19-0d0c-43a7-8e3f-929108a7f214", //sessionId
    "t": "vst",                                                       //这个值也是写好的，是用户行为的初始值
    "tm": 1462865639647,                                 //代表时间
    "sh": 720,                                                     //screenHeight 屏幕的高度
    "sw": 1280,                                                  //screenwidth 屏幕的宽度
    "d": "localhost:8080",                                    //获取主机名
    "p": "/apm-agent-load/index.html",            //路径名
    "rf": "",                                                          //document.referrer 通过<a>标签访问前面的页面的URL
    "l": "zh-cn"                                                   //浏览器所使用的语言
}, {
    "u": "3a27ab7a-68fd-4c77-92f7-514e334f7fbd", //userId
    "s": "37e38b19-0d0c-43a7-8e3f-929108a7f214", //sessionId
    "tl": "Insert title here",                                          //当前文档的标题
    "t": "page",                                                          //这个值是写好的就是page
    "tm": 1462865639647,                                         //代表时间
    "pt": "http",                                                          // 设置或获取 URL 的协议部分
    "d": "localhost:8080",                                           //获取主机名
    "p": "/apm-agent-load/index.html",                     //路径名
    "rf": ""                                                                      //document.referrer 访问前面的页面的URL
}]

[{
    "u": "3a27ab7a-68fd-4c77-92f7-514e334f7fbd",
    "s": "37e38b19-0d0c-43a7-8e3f-929108a7f214",
    "t": "imp",    //捕获事件例如click的事件
    "tm": 1462865644120,
    "ptm": 1462865639647,
    "d": "localhost:8080",
    "p": "/apm-agent-load/index.html",
    "e": []
}]
e就是观察dom的节点信息
"e": [{
                    "x": " / a ",//标签名
                    "  v ":" abutton ", //文本
                    " h ":" http: //www.baidu.com" //链接
         },
         {"x":"/form",  //标签名
                     "v":"用户名:" //文本
          }
          ]
点击事件的时候会向后台发送请求，放松的请求的json是，说明是一个clck的请求
[{
        "u": "3a27ab7a-68fd-4c77-92f7-514e334f7fbd",
        "s": "846e484c-cfa5-4ee5-a76c-271b7609ff15",
        "t": "clck",
        "tm": 1462950220791,
        "ptm": 1462950194249,
        "d": "localhost:8080",
        "p": "/apm-agent-load/index.html",
        "e": [{
                    "x": " / form ","
                    v ":"
                    用户名: "}]}]
10)utils
11)cookie
这个函数定义了获得cookie中的信息具体的方法例如
getItem，setItem，removeItem，hasItem，keys
12)
13)lzstring 压缩成不同的编码格式，这个压缩了发送请求的时候就会成乱码的形式
compressToUTF16
compressToUint8Array
compressToEncodedURIComponent
compress
_compress
14)tree-mirror
