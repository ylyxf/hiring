### 在vue文件的template下，可以不经引用直接使用第三方库在组件

看起来所有的引用，都是由入口main.js引入的，在vue文件中，直接使用即可。和java的`import`、c的`include`、react的`import`，风格不太一样。
这样带来一个问题，我无法通过vue文件的头部，一下子知道vue文件中，引入了哪些组件。

实际上，template下可以使用哪些组件，和script标签也有关系。script标签`export default`对象的components属性，应该是包含了template下的可用组件。
那么Vue.use是全局注册的意思吗？

---

### 错误 You are using the runtime-only build of Vue where the template compiler is not available

引入easyui时报了这样一个错误，原因是import会依赖引入目标文件夹下的package.json的main字段，来决定引入那个具体的js。
vue有支持模板的js，也有不支持模板的js，默认引入的是不支持模板的js，导致出错了。（但是为什么element-ui没有出错？）
解决办法是通过webpack的别名机制，将关键`vue`指向支持模板的`vue/dist/vue.esm.js`:
在项目根目录下，新建vue.config.js
<pre>
module.exports = {
 
  configureWebpack: config => {
    config.resolve = {
      extensions: [".js", ".vue", ".json", ".css"],
      alias: {
        vue$: "vue/dist/vue.esm.js",
        "@": resolve("src")
      }
    };
  }
 
}; 
</pre>
简单的写法就是：
<pre>
module.exports = {
    runtimeCompiler: true,
}
</pre>


### 解析csv到json

使用papaparse库，可以将csv解析为json，调用parse函数时，有一个header属性，要设置为true，否则category读出来是二维数组而不是json数组。


### 将数据更新到表格

最神奇的是将数据设置到表格中，也就是双向绑定之：通过数据改变视图。


对比不绑定的情况，比如jquery的组件库，通常在需要更新的时候，用`$('#xxx')`去获得组件，然后组件.setData();来更新视图，或者干脆`$('#xxxx')`获得html元素，然后`元素.html(data)`或者`元素.text(data)`。这种方法有着强大的控制力，指哪儿打哪儿。

但是在场景复杂的情况下，会面临一个窘境：比如一个下载列表，列表中的每个任务，都有进度、状态，列表上方还要有个总进度和实时网速。每当一个任务的一小段数据下载完成后，我们的业务代码,要计算当前任务的进度、状态、所有任务的总进度和实时网速等数据。计算完成后，还要更新界面上单条任务的进度、状态，还要记得更新总进度的显示数字，还要记得更新实时速度的显示。现在看来，当一个事件发生后，响应这个事件的代码，不但要根据事件计算数据，还要负责根据数据，去重新渲染各种视图，代码一大坨，越写越多。最要命的，是如果有新的下载任务加进来，对`新增下载任务`这个事件的响应代码，也要更新任务列表的总进度，暂停、删除某个任务的事件发生后，除了更新任务自身的显示状态，也要更新任务列表上显示的总进度....

软件领域的"银弹"来了，分层。

我们多分成一个数据层，也就是在内存里划一块区域，这里放着组件显示上需要的所有数据。我们的代码也分成两部分，一部分是响应事件的代码，它只管根据事件和内存中现有的数据去计算，把结果更新到这块内存；
另一部分是根据数据更新视图的代码，也就是将视图组件和这块内存的数据进行绑定。这个绑定关系我们写好后，交给一个我们雇佣的一个不知疲倦的人，他实时地检查数据，根据数据的变化去更新视图。

那么，我们雇佣的不知疲倦的人，要怎么做更新视图的工作？我能直接想到的最朴实的办法，交给一个线程，不断地去读这块内存的数据，去更新视图。这个方案的好处是实现简单，而且契合UI界面应该只有一个非业务工作线程来处理的原则。这个原则是我编的，`只有一个`是避免多线程操作UI一起混乱，`非业务工作线程`是避免长时间工作导致界面更新迟钝（注意不是卡顿，卡顿那个事儿我还没想明白）。

缺点是效率低下，体现在这个线程不管数据变没变，都一股脑地去更新视图组件。如果视图组件自己有缓存机制，判断即将要更新的内容和自己当前显示的内容一致，就不更新了，那还好；如果是基于html绘制的grid组件，有没有缓存很难说，要看组件作者的具体实现。最好这个佣人能自己搞定这些乱七八糟的事情，vue提供的这个佣人，使用了js语言的Observer特性，具体咋实现的还待研究。

> 代码分开了，但是之前问题里的一个隐患还没解决：当总任务列表上的数字显示不正确的时候，到底是响应哪个事件的业务代码造成的问题？（待续）


通过数据更新视图的前提是，要把预先定义的数据和视图绑定，然后框架记住这个绑定关系，当后面再改变数据时，框架来完成视图的更新。

在javafx中是在代码中写bind，fxml好像不支持双向的绑定方式，代码写得很丑。在vue中，用标签的属性进行绑定，很漂亮；react用jsx来进行绑定，也算蛮漂亮。

但是这种漂亮，在单层组件上是值得欣赏的，但一个完整的用户界面，是由一棵组件树组成的，更甚者，像eclipse这种变态的perspective特性，views可以有多种组合，树枝之间可以乾坤大挪移，是一棵动态的树。

也就是说，即使我们设计了一个很好的下载列表，但是当下载列表嵌入到整个软件的界面时，又有问题了：

因为某种业务规则，一般会员只能同时开启6个任务，在下载列表组件之外，有个用户名组件，用户名组件旁边有个上限数字（2/6）,这里的6表示是初级会员只能同时开6个任务，而2表示当前正在进行的有2个任务。
为了界面炫酷易用，在桌面上会悬浮一个进度球，上面显示的是下载速度，对，和下载列表上的那个下载速度是一个数。

随着系统功能迭代，事件响应的业务代码，在更新数据的时候，又回到之前的问题了，数据散落在各组件中，要去更新各组件的数据。

本质是，组件不能光依赖内部的数据，还要依赖外部的数据。

如果整个软件看做是一个大组件，数据都放在大组件里，就免去了事件响应业务代码去更新各数据源之苦，但是又带了另一种苦，组成树形结构的各组件，如何依赖这个统一的外部数据？

组件是预制的，是可复用的，一个List组件，在和软件A结合的时候，要能绑定A的全局数据，在和软件B结合的时候，要能绑定B的全局数据。List组件不能预知一切，它要面向变化，所以它应该提供props，让数据能注入到组件中。

全局数据，怎么能注入到孙子组件中呢？按照我的理解，在浏览器中，window.data就能解决，但是还是那个复用的问题，也许基于List之上，又封装了一个GreatList组件，它包含了List组件。那么List组件还能用window.data吗？如果有多个List，怎么知道window.data.listA数据和哪个List绑定？逐层传递，可能是唯一的办法。

//写不下去了...





 

