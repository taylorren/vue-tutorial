# 幻灯片组件

接下来我们要实现“幻灯片组件”。这个组件又由四个独立组件完成，分别显示：最近购买的书籍、最近评论的书籍、随机选择的书籍和好久没有访问的书籍。

## 幻灯片的responsive显示

由于幻灯片是一个“桌面”专属的属性，我们需要对这段内容进行处理，在“桌面”和“手机”配置下，分别显示不同的内容。

在“桌面”配置下，该段内容是四个幻灯片循环显示：

![](http://rsywx.com/lib/exe/fetch.php/vue:04-01.png)

如果进入“手机”配置（屏幕宽度不那么大），该段内容将是简单的一个欢迎标语：

![](http://rsywx.com/lib/exe/fetch.php/vue:04-02.png)

参照我使用的模板的写法，我将这两个不同的内容直接再包在一个`<div>`中：

```
// /src/components/Slider.vue

<template>
  <div>
    <!--LayerSlider begin-->
    <!-- Note: Layerslider is configured in js/grove-slider.js -->
    <div class="hidden-xs">
        <div id="layerslider" style="width: 100%; height: 405px; margin: 0px auto;">
            <LatestBookSlide/>
            <LatestReadingSlide/>
            <RandomBookSlide/>
            <ForgottenBookSlide/>
        </div>
    </div>
    
    <!--LayerSlider end-->
    <!-- Layerslider substitute on x-small devices -->
    <div class="widewrapper pagetitle visible-xs">
        <div class="container">
            <h1>欢迎来到任氏有无轩！</h1>        
        </div>
    </div> 
  </div>
</template>
```

读者应该注意到，常规桌面显示时，四张幻灯片会被分为四个小组件：

```
    <!--LayerSlider begin-->
    <!-- Note: Layerslider is configured in js/grove-slider.js -->
    <div class="hidden-xs">
        <div id="layerslider" style="width: 100%; height: 405px; margin: 0px auto;">
            <LatestBookSlide/>
            <LatestReadingSlide/>
            <RandomBookSlide/>
            <ForgottenBookSlide/>
        </div>
    </div>
```

其中，`LatestBookSlide`, `LatestReadingSlide`, `RandomBookSlide`和`ForgottenBookSlide`分别显示幻灯片组件中的一幅幻灯片，对应我们想要显示的四个内容。

## 实现LatestBookSlide

我们挑选一个组件来详细讲解如何实现。我们会看到如何调用远程API来获得数据，并触发页面的刷新。

Vue的组件有自己的生命周期，详细的介绍可以参见官方文档。

大部分情况下，我们用到的是组件的`created`这一生命周期相关事件。此时，该组件所有的DOM节点已经被置入整个HTML文档的DOM树中，但是相应的动态内容可能还没有被获取。这是一个恰当的时机来获取远程API的数据。

### 获取远程API数据

Vue和JS支持原生的异步远程API调用((此时需要注意，远程API要支持JS的跨域调用。实现JS跨域调用有很多方式。在本实例中，由于提供API服务的服务器也在作者控制之下，所以采用的是比较简单的在API头中加入CORS的方式。))。ES6开始支持`await`关键字，但是为了保持与浏览器的兼容性，我们采用比较保守的`then`方式：

```
<script>
export default {
  name: "LatestBookSlide",
  data() {
      return {
          book: [],
      };
  },
  computed: {
      coveruri: function() {
          return 'http://api.rsywx.com/book/cover/'+this.book.bookid+'/'+this.book.title+'/'+this.book.author+'/300';
      }

  },
  created: function() {
    this.getLatestBook();
  },
  methods: {
      getLatestBook() {
        var uri='http://api.rsywx.com/book/latestBook';
        fetch(uri)
            .then(res =>{
                return res.json();
            })
            .then(json => {
                this.book=json.out[0];
            })
      }
  }
};
</script>
```

`fetch`函数返回一个promise，这是一个异步对象。然后我们对返回的内容进行解析。这个解析一般分为两步。

第一步是获得JSON方式表示的返回内容（`return response.json()`）。

第二步是对该JSON字符串进行解析，并设置相应的数据（`this.book=json.out[0]`）。

值得注意的是，我们在这段代码中，根据返回的数据“计算”而得到了一个数据`coveruri`。这个数据在模板中会被使用。

**2017年11月23日更新：**安置多个幻灯片组件后，发现第二、第三个幻灯片组件的计算属性没能得到更新——虽然各个幻灯片组件已经正确地获得了数据并正确地显示在了对应的位置。所以，我不再纠结于“政治正确”的做法，改为更直接了当的写法：

```
export default {
  name: "RandomBookSlide",
  data() {
    return {
      book: [],
    };
  },
  /*computed: {
    coveruri: function() {
      return ("http://api.rsywx.com/book/cover/"+this.book.bookid+"/"+this.book.title+"/"+this.book.author+"/300");
    }
  },*/
  created: function() {
    this.getRandomBook();
  },
  methods: {
    getRandomBook() {
      var uri = "http://api.rsywx.com/book/randomBook/1";
      fetch(uri)
        .then(res => {
          return res.json();
        })
        .then(json => {
          var book=json.out[0];
          var coveruri='http://api.rsywx.com/book/cover/'+book.bookid+'/'+book.title+'/'+book.author+'/300';
          book.coveruri=coveruri;

          this.book=book;
        });
    }
  }
};
```

### 调整模板

我们是按照模板在进行操作，所以其中的大部分内容都可以直接从模板中拷贝过来。

在需要显示动态内容的时候，我们可以这样做：

```
<div><p class="mid"><router-link class="yellow shadow" :to="{name: 'BookDetail', params: {id: book.bookid} }">{{book.title}}</router-link></p></div>
<div><p className="s18">{{this.book.author}</p></div>
<div><p className="s18">{{this.book.p_name}</p></div>
<div><p className="small"><em>收录时间：{{this.book.purchdate}}</em></p></div>
```

值得注意的地方是，幻灯片图片相应HTML属性的设置：

```
<img  class="ls-l" :src="book.coveruri" :alt="book.title" :title="book.title"
  style="top:15px; left:650px;"
  "/>
```

由于Vue不允许在这样的HTML属性中直接使用`{{...}}`赋值的方式，我们采用的是属性绑定的语法。

就是这样，我们很快地完成了幻灯片组件中的一个子组件的开发。其它三个子组件的开发方式与此类似。不再赘述。请读者自行完成。

注意：本应用使用到的API接口在[http://api.rsywx.com](http://api.rsywx.com)中可以找到文档说明。调用方式为：`http://api.rsywx.com/module/method`，将`module`和`method`替换为对应的模块名（大小写不敏感）和方法名（大小写敏感）即可。

另外，当然我们也修改了路由等相关的文件，放入了书籍详情（`BookDetail`）的路由配置信息。