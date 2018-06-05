# 安装

## 安装NPM

NPM的全称是Node Package Manager，它是Node.js所采用的包管理器。

现代意义上的程序开发，已经没有“单个程序，独立成体”的可能。在Web开发领域，程序开发依赖于框架，而框架又依赖于各个功能部件为程序的开发和运行提供支持。

熟悉Ubuntu系统的读者应该知道，Ubuntu用apt这个软件包管理器来管理各程序包之间的依赖关系，省却了用户人工管理的麻烦。

为了解决Web开发中用到的各个功能模块（称为“包”，package）之间的依赖关系，主流的框架都会有相应的包管理解决方案。比较出名的有composer和npm。

在node.js的[官网](https://nodejs.org/en/)可以找到相应的node.js安装程序。需要注意的是，node.js目前有两大版本，一个是8.9.1的LTS版本，一个是9.2.0的当前版本。我使用的是LTS版本。

node.js安装完毕后，也会安装好npm，并设置好相应的环境变量。

我们可以打开一个命令行，运行一下`npm -v`和`node -v`，看到如下信息后就可以确信npm和node已经安装完毕：

![](http://rsywx.com/lib/exe/fetch.php/vue:01.01.png)

## 安装yarn（可选）

建议安装Yarn。它是一个包的缓存管理程序。如果我们要大量利用React开发程序，有很多包必然是重复使用的。有了Yarn之后就可以免除重复下载的工作，提高应用创建的速度。

## 安装vue-cli

我建议使用`vue-cli`作为应用开发的自举入口，所以我们需要安装这个程序包：

>npm i -g vue-cli

** 注意：**如果是在Windows操作系统下，有可能需要在管理员权限下执行上面这个命令。

`vue-cli`安装完毕后，我们就完成了使用Vue进行Web开发的所有必要程序的安装。

## 创建我们的应用

在某个目录中（我的是`d:\Resources\Vue`）运行`vue init webpack rsywx`命令。该命令会在当前目录下创建一个`rsywx`的目录，同时创建一个`rsywx`应用，并为我们创建必要的入口程序等。这个过程会比较长，期间有大量的网络操作和提示输出。一般我会选择安装`vue-router`——对于一个复杂的Web应用来说，没有路由也是不可想象的。

![](http://rsywx.com/lib/exe/fetch.php/vue:01.02.png)

等待应用创建完毕后，可以进入这个目录，用`npm install`来安装所有的依赖关系。用资源管理器浏览这个`rsywx`目录，会看到如下的内容：

![](http://rsywx.com/lib/exe/fetch.php/vue:01.03.png)

我们创建的应用目录中有一些是比较重要的：

>`node_modules`：这里保存着所有本应用开发时需要用到的node.js开发模块。
>`src`：我们的编程基本上在这里进行。
>`static`：站点用到的相应支持文件（如CSS、图片、JS库等）都会放在这里。

值得注意的是，Vue将`index.html`放在根目录下。

运行一下！

进入`rsywx`目录，在命令行中输入`yarn start`（或者`npm start`）。命令行中会出现类似如下的提示：

![](http://rsywx.com/lib/exe/fetch.php/vue:01.04.png)

并启动Web浏览器，在localhost:8080这个地址启动我们的第一个Vue应用：

![](http://rsywx.com/lib/exe/fetch.php/vue:01.05.png)

OK！我们的第一个Vue应用已经完成了！

在后续的教程中，我们会在这个基础上逐步修改、增加我们的内容。