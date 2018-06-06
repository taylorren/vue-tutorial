# 路由

## 路由

Vue采用的是单文件入口形式。换句话说，我们在访问一个类似`https://rsywx.net/books/list`这样的页面的时候，没有一个“实际存在”的这样的页面。和其他框架类似，Vuet会通过“分发”（dispatch）的方式来解析这个请求，并生成相应的页面。

在这样的分发和调度过程中，起作用的就是通常所说的“路由”（routing）。

### 安装路由支持

安装了`vue-cli`，并用`vue init webpack rsywx`创建我们的应用过程中，有一个问题是“是否要安装vue-router”。一般情况下，我们都要选择安装这个部件。`vue-router`是Vue官方的路由组件。有关这个组件的详细说明，可以参见其[官方文档](https://router.vuejs.org/zh-cn/)。在本教程中，我们在需要的地方也会做一些说明。

## index.html

我们首先来看一下`index.html`文件。它位于项目根目录下。

由于JavaScript是客户端语言，所以基于JS框架开发的Web应用只能用`index.html`作为应用的入口。

`/index.html`这个文件最重要的内容是如下这一段：

```
<body>
    <div id="app"></div>
</body>
```

可以说这个文件没有任何内容！只有一个`id`为`app`的空`<div>`而已！

既然这个文件是我们应用的入口文件，我们应该对这个文件进行修改，加入必要的样式文件和其他JS脚本文件[^1]，构造出所有“页面”所依赖的外部文件架构。

在我的“任氏有无轩”应用中，我购买了一个商业用模板。于是我需要将我模板所需要的外部静态文件和结构复制到这个`index.html`中。

修改后的文件内容大致如下：

```
<!DOCTYPE html>
<!--[if lt IE 7]>      <html class="no-js lt-ie9 lt-ie8 lt-ie7"> <![endif]-->
<!--[if IE 7]>         <html class="no-js lt-ie9 lt-ie8"> <![endif]-->
<!--[if IE 8]>         <html class="no-js lt-ie9"> <![endif]-->
<!--[if gt IE 8]><!-->
<html class="no-js"> <!--<![endif]-->
<head>
    <!-- Bootstrap styles -->
    <link rel="stylesheet" href="/node_modules/bootstrap/dist/css/bootstrap.min.css" type="text/css">

    <!-- Glyphicons -->

    <!-- Google Webfonts -->

    <!-- LayerSlider styles -->

    <!-- Grove Styles (switch for different color schemes, e.g. "styles-cleanblue.css") -->

    <!-- RSYWX CSS -->
    <link rel="stylesheet" href="/css/rsywx.css" id="rsywx-styles">

    <!--[if lt IE 9]>

    <![endif]-->

    <script src="//code.jquery.com/jquery-1.11.3.min.js"></script>

    <!-- LayerSlider from Kreatura Media with Transitions -->

    <!-- Grove Layerslider initiation script -->
</head>
<body>
  <div id='root'></div>
  <script src="/node_modules/bootstrap/dist/js/bootstrap.min.js"></script>
  <script src="/modules/modernizr/modernizr-custom.js"></script>
</body>
</html>
```

如果你对Bootstrap这个模板框架比较熟悉，你会发现这不过是一个Bootstrap模板的常规结构。

在这个文件中我加入了我自己编写的针对本站点使用的`rsywx.css`。

此时，我们应该在`/`目录下根据我们选用的Bootstrap框架的要求创建不同的目录（如`css`、`js`、`img`等），并将Bootstrap框架要求的文件分别拷贝到这些目录中去。

做完上面的工作后，我们的`/`目录结构会是这样的：[^2]

![](http://rsywx.com/lib/exe/fetch.php/vue:02-01.png)

注意，这个`index.html`文件还是没有任何实质性的内容！还是只有一个`id`为`root`的空`div`而已！

## src文件夹

我们再来看下现在的`/src`文件夹中有些什么东西：

  * `App.vue`：应用入口的组件文件
  * `main.js`：应用的自举入口
  * `router/index.js`：路由配置文件
  * `components/HelloWorld.vue`：组件文件

### App.vue

我们先看看`vue-cli`为我们创建的内容是些什么：

```
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

这个文件由三部分组成：

  - 第一部分定义了整个应用的布局。它必然要包括一个`id="app"`的`div`块。这里将是我们日后进行模板编写的地方。在目前的布局中，它有两个部分：
    - 一个是一个图片，我们可以将其删除。
    - 一个是`<router-view/>`。这个是我们设定应用要用到路由后，`vue-cli`为我们加上的。**这个不能拿掉。**
  - 第二部分是一个`<script>...</script>`，这里将是我们日后进行控制的地方。现在我们可以不用修改这里的任何内容。
  - 第三部分是一个`<style>...</style>`，这里可以设置样式。由于我们完全采用外部样式，这一部分可以删除。

这个文件只做了一件事情：定义了整个应用的页面布局。而一般情况下，对于使用路由的应用，我们只要在这里加入一个`<router-view/>`就可以了。至于这个`<router-view/>`又是什么，我们稍后会讨论。

### main.js

现在的`/src/main.js`文件是这样的：

```
import Vue from 'vue'
import App from './App'
import router from './router'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  template: '<App/>',
  components: { App }
)
```

这个文件的主要任务，就是将我们的应用进行自举式配置。我们不用对此进行任何修改。但是需要注意构造函数中的四个参数与其它文件中要素的关联：

  * `el: '#app'`中的`#app`对应着的是：`App.vue`中的那个`<div>`的id。
  * `router`是我们定义的路由配置（见下一小节）。
  * `template`指向我们要用哪个组件作为模板，也就是`App.vue`文件中定义的组件。
  * `components`指向我们要用那个组件来进行控制，也就是`App.vue`。

一般情况下，我们不用修改这个文件。

### 路由配置文件

Vue将路由配置文件放置在`src/router`的`index.js`中：

```
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    }
  ]
}) 
```

`HelloWorld`作为一个样板程序中的一个页面当然不错，不过为了我们应用的需要，我们将其修改为`Home`，并调用`Home`组件（下一节我们会修改这个现有的组件文件）：

```
import Vue from 'vue'
import Router from 'vue-router'
import Home from '@/components/Home'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'Home',
      component: Home
    }
  ]
})
```

**注意：**做出这个修改并保存后，我们的应用无法运行了——因为我们并没有修改相应的文件，所以应用找不到我们需要的`Home`组件。

###components/HelloWorld.vue => Home.vue

我们先将`HelloWorld.vue`更名为`Home.vue`，然后打开文件：

```
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>Essential Links</h2>
    ...
  </div>
</template>

<script>
export default {
  name: 'Home',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
...
</style>
```

我们可以很快地删除`<style>...</style>`。对于`<template>...</tempalte>`我们可以删除其中的内容，而暂时用一段简单的文字替代。而对于`<script>...</script>`字段，我们先将其`name`属性改成`Home`。保存修改后，我们的应用又可以重新显示。说明我们的修改没有错误。

## 测试运行

我们现在再看看效果。

如果有必要，在命令行中输入`yarn start`，我们的程序就会得到运行，一个新的浏览器窗口会打开并浏览到`localhost:8080`，显示如下的页面：

![](http://rsywx.com/lib/exe/fetch.php/vue:02-02.png)

如果你也能看到这个界面，那么恭喜你！

我们已经开了一个非常好的头，开发了一个可以真正运行的Vue网页！

**注意：**也许你注意到我们的URI中多了一个''#''。这是Vue框架的一个约定。但是这个''#''放在URI里不是很好看。我们修改`src/router/index.js`，在路由构造中加入这么一行：

```
import Vue from 'vue'
import Router from 'vue-router'
import Home from '@/components/Home'

Vue.use(Router)

export default new Router({
  routes: [
  ...
  ],
  mode: 'history'

})
```

这样我们应用的URI中就不会有这个古怪的`#`了。

让我们用一张图来总结一下我们到目前为止学到的东西。

![](http://rsywx.com/lib/exe/fetch.php/vue:02-03.png)

[^1]: 由于Vue以及其他一些Node框架（React除外）的限制，外部JS的加载会很麻烦。所以在这里我们把一些特别的第三方JS库先不加进来。

[^2]: 注意，两个JS文件会被拷贝到由Vue创建的`node_modules`之下。