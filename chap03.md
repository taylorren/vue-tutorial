# 主页

我们现在的主页（`Home.vue`）还十分简陋，只有一个简单的欢迎字符串。这只是用来表明我们在前一篇“[路由](https://rsywx.gitbook.io/vue/lu-you)”中所定义的路由（到页面）的解析已经完成。

我们在这一章和后续几章将详细分析：

  * 如何设计一个界面并将其分割为合理的“组件；
  * 如何设计各个组件的实现；
  * 如何进行异步远程API的调用；
  * 如何对状态进行设置和读取；
  * 如何基于状态的变化对页面进行渲染。

本章以及后续的几章是最为重要的内容。

## 界面设计和分割

我们先来看看完成的首页应该是个什么样子。如下的屏幕截图来自目前处于发布状态的“任氏有无轩”首页：

![](http://rsywx.com/lib/exe/fetch.php/vue:rsywx.net-symfony.png)

这是一个典型的由上到下的页面布局。几乎内容都是动态的，来自页面本身的变量或者远程的API返回内容。我们可以直观地将其分为如下几个部分：

  * 顶部导航栏
  * 站点Logo部分。静态。
  * 导航部分。根据当前所在页面，高亮显示目前所在位置。((因为导航栏显示的部分链接会引向外部站点，所以，这个高亮其实只对部分页面有用。))
  * 幻灯片。这里会展示四个不同的内容（截图中没有显示）：
      * 最近买的书；
      * 最近读的书；
      * 随便挑的书；
      * 好久没有访问的书；
  * 任氏有无轩的摘要信息：
      * 藏书信息；
      * 读书信息；
      * 博客信息；
      * 维客信息（静态）；
  * 历史上的今天
  * 最近的三篇博客文章
  * 随机推荐的三本书籍
  * 底部信息栏（版权、社交信息、安全声明）
  * （不可见）的页面元信息（MetaData）

简单地算算，我们会有十几个组件。为了兼顾视觉结构和功能结构，我们会这样来设计我们的结构层次：

![](http://rsywx.com/lib/exe/fetch.php/react:03-02.png)

作为一个简单的映射，我们可以简单地认为：这里的每个框都对应着一个组件，由一个Vue组件去实现。

## 由顶向下的设计

在Vue应用设计过程中，我推荐使用由顶向下的设计过程。我们总是从最顶层的、可能包含更多子组件的组件开始，规划层级关系，设定布局要素。

我们修改`/src/components/Home.vue`文件如下：

```
<template>
  <div>
    <TopNav/>
    <Slider/>
    <Intro/>
    <Today/>
    <Blog/>
    <Random/>
  </div>
</template>

<script>
import TopNav from '@/components/TopNav';
import Slider from '@/components/Slider';
import Intro from '@/components/Intro';
import Today from '@/components/Today';
import Blog from '@/components/Blog';
import Random from '@/components/Random';

export default {
  name: "Home",
  components: {
    TopNav,
    Slider,
    Intro,
    Today,
    Blog,
    Random,
  }

};
</script>
```

请特别注意几点：

  - React和Vue一样，只能渲染一个“根”节点。所以在复杂布局的情况下，要将所有节点硬性封装在一个空的`<div>`中。
  - 为了在一个vue组件中成功地引用各个外部子部件，我们要做三件事：
    - 在`<template>`中恰当地引用。
    - 在`<script>`中加以''import''。
    - 在模块声明的`componnents`部分进行声明。

这个文件还无法通过编译，因为有很多“子组件”缺失。我们可以仿照之前的方式，生成这些文件（并放置在对应的目录中）。而这些子组件目前只返回一个简单的`<p>...</p>`段落，用于提供这些组件会在哪里显示的视觉反馈而已。比如这个`/components/Slider.vue`文件：

```
<template>
  <p>This is Slider section</p>
</template>

<script>
export default {
  name: "Slider",
};
</script>
```

**注意：**我们并没有在首页的布局中放入“脚注（footer）”，这是因为页面脚注部分全部是静态内容，所以我决定直接放在`/src/App.vue`中。我直接将相应商业模板文件中的“脚注”部分拷贝过来：

```
    <router-view/>
    <footer class="widewrapper footer">
        <div class="container">
        ...
        </div>
    </footer>
```

### 页面的<meta>

我们还要用到`vue-meta`这个Vue部件，用来操作页面的元信息，因此我们用`yarn add vue-meta`来安装这个依赖关系。

由于我们可以预见到会在所有页面中用到`<meta>...</meta>`部件，所以我们可以进行全局加载，方法是在`/src/router/index.js`中加入一行：

```
import Meta from 'vue-meta'
...

Vue.use(Router)
Vue.use(Meta)

export default new Router({ 
...
})
```

然后修改我们的`Home.vue`文件：

```
<template>
  <div>
    <TopNav :active='1'/>
    <Slider/>
    <Intro/>
    <Today/>
    <Blog/>
    <Random/>
  </div>
</template>

<script>
...

export default {
  name: "Home",
  ...
  data() {
  },
  metaInfo: {
    title: "任氏有无轩 | 藏书、读书、博客、维客、资源",
    htmlAttrs: {
      lang: 'zh-CN'
    },
    meta: [
      {name: 'description', content: "任氏有无轩主人精心打造的一个个人站点。"},
      {name: 'keyword', content: "个人站点,藏书,读书,博客,维客,资源"}
    ]
  }  
};
</script>
```

如果一切都如我们所愿，在完成上述目录和文件的创建后，我们就可以用`yarn start`命令来看看效果：

![](http://rsywx.com/lib/exe/fetch.php/vue:03-02.png)

我们看到，所有页面的动态部分都已经成功地展示了——虽然没有任何样式的修饰，而脚注部分也得以呈现并且配上了样式。这是很了不起的第一步。我们完成了一个页面的分割和组合。

## Vue的DOM渲染

在进入更深层次的编程之前，我们来看看这个页面到底是什么东西。

在FireFox中按下`Ctrl-Shift-I`，可以调出FX内置的调试器。这是一个非常强大的HTML、JS调试器，如果你还从没有使用它或者不熟悉它，建议花一些时间去熟悉一下这个工具。

![](http://rsywx.com/lib/exe/fetch.php/vue:03-01.png)

从图片中我们可以清楚地看到：

  * 我们在`/index.html`中创建的那个`<div id="app">`被原封不动地转到了最终页面中。
  * Vue在该root节点下，生成了若干DOM节点并形成了一棵新的树。
  * 我们在各个组件中提供的内容作为该树的子节点，依次显示在树中。

注意：不要用浏览器的“查看页面源代码”的方式来查看Vue页面。所有的“内容”都是Vue对DOM动态操作而生成的，所以“查看页面源代吗”根本看不到这些动态生成的节点。

注意：FX内置的调试器已经足够强大，并能给出很多关于DOM节点的信息。

接下来我们会依次实现这些组件。