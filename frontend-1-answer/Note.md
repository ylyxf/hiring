### 在vue文件的template下，可以不经引用直接使用第三方库在组件

看起来所有的引用，都是由入口main.js引入的，在vue文件中，直接使用即可。和java的`import`、c的`include`、react的`import`，风格不太一样。
这样代理一个问题，我无法通过vue文件的头部，一下子知道vue文件中，引入了哪些组件。