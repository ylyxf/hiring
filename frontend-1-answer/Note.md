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
