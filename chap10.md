# 书籍列表

在“书籍列表”页面中，我们要完成如下几件事情：

  * 分页显示书籍；
  * 根据关键词（目前只支持书籍名称开头字符）搜索书籍；
  * 直接输入分页页数跳转；
  * 进行前后页跳转；

## 分页显示书籍

这其实是很容易实现的功能。我们的远程API接口有一个函数：`listBook`，它接受几个参数：

  * 搜索类型（type）：目前支持title和tag，分别按照书籍名称的开始部分字符搜索和tag搜索。缺省为title。
  * 搜索关键词（key）。缺省为all，即所有书籍。
  * 第几页（page）：显示第几页。缺省为1。

为了显示这个页面，我们需要继续配置路由。

在`/src/router/index.js`中加这样一个路由：

```
{
  path:'/books/list/:search/:keyword/:page',
  name: 'BookList',
  component: BookList,
  props: true
},
```

## 编写BookList组件

我们已经掌握了编写该组件的大部分知识和内容，需要特别指出的是获得路由中的参数，而这是通过如下代码实现的：

```
export default {
  name: "BookList",
  data: function() {
    return {
      pages: -1,
      books: [],
    }
  },
  components: {
    TopNav,
    Paginator,
  },
  props: [
    'search',
    'keyword',
    'page',

  ],
  created: function() {
    this.getBooks(this.search, this.keyword, this.page);
    console.log(this.books);
  },
  methods: {
    getBooks(search, keyword, page) {
      var uri="http://api.rsywx.com/books/list/"+search+"/"+keyword+"/"+page;
      console.log(uri);
      fetch(uri)
        .then(res => {
          return res.json();
        })
        .then(json => {
          var out=json.out;
          this.pages=Math.ceil(parseInt(out.count.bc)/20);
          this.books=out.books;
        })
    }
  },
};
```

这里的参数都是通过路由匹配而来，直接由组件的''props''属性来获取。

数据的获取我们还是在`created`中实现：

很好！只要完成这一步，这个组件的主体就基本完成了。

## 编写分页组件Paginator.js

我有很多藏书，所以需要进行分页显示。这里我采用的是比较简单的分页方式，只出现“首页”、“上一页”、“下一页”和“最后一页”这四项，而不是出现若干分页数字直接跳转（这个功能另外实现了）：

![](http://rsywx.com/lib/exe/fetch.php/react:10-01.png)

在``BookList`组件中，分页组件的调用是这样完成的：

```
<Paginator :search="search" :keyword="keyword" :page="page" :pages="pages"/>
```

分页组件必须根据当前的搜索（列表）环境构造才有意义。所以我们向该组件传递了当前URI的所有参数。

`Paginator`组件的编写其实比较简单，只是有一些条件控制罢了。比如，在第一页的时候，“上一页”的链接应该是不可用的：

```
<section id="pagination" class="col-md-12">
  <a :href="firstpage" title="首页"><i class="glyphicons fast_backward"></i></a>
  <a v-if="page==='1'" class="disabled" title="上一页"><i class="glyphicons rewind"></i></a>
  <a v-else :href="prev" title="上一页"><i class="glyphicons rewind"></i></a>
  <a v-if="parseInt(page)===parseInt(pages)" class="disabled" title="下一页"><i class="glyphicons forward"></i></a>
  <a v-else :href="next" title ="下一页"><i class="glyphicons forward"></i></a>
  <a :href="lastpage" title="末页"><i class="glyphicons fast_forward"></i></a>
</section>
```

这里为了方便，我用了若干计算属性，并用`v-if`来渲染不同的导航标记。

## 关键词搜索

目前我们的应用还只支持从书籍名称开始搜索，比如在搜索框中输入“红”就会显示所有以“红”开头的书籍：

![](http://rsywx.com/lib/exe/fetch.php/react:10-02.png)

这个功能的实现在Vue下比较简单，比[React相应部分](https://rsywx.gitbook.io/react/shu-ji-lie-biao#guan-jian-ci-sou-suo)简单多了。

首先是HTML部分的编写，我们会创建一个HTML的表单，来处理关键词的搜索：

```
<section id="searchform" class="col-md-6">
    <form accept-charset="utf-8" class="form-inline">
        <div class="row">
            <div class="col-sm-6 form-group">
                <input class="form-control input-sm" type="text" id="key" name="key" placeholder="all" v-model="searchfor">
                <button type="submit" class="btn btn-grove-one btn-sm btn-bold" @click="searchPage">搜索</button>
            </div>
        </div>
    </form>
</section>
```

这里有两个新的语法。一个是`v-model`，用来将表单中的一个字段与组件中的某个属性进行双向绑定；另一个是`@click`，用来设定一个单击处理事件函数。该函数如下：

```
searchPage(e) {
    window.location="/books/list/title/"+this.searchfor+"/1";
    e.preventDefault();
}
```

React的相应功能实现，基本与此类似，但是有`this`指针的考虑。在这点上，Vue要直接了当多了。

输入页码后跳转到某个特定页面的写法也基本类似。这里不再赘述。请读者自行完成。

我们回顾一下，我们已经完成了：

  * 分页显示书籍；
  * 根据关键词（目前只支持书籍名称开头字符）搜索书籍；
  * 直接输入分页页数跳转；
  * 进行前后页跳转；

## 根据一本书籍的tag选择具有该tag的书籍

在前一章“书籍详情”中，我们提到，点击一本书的某个tag，就会显示所有也被标记为那个tag的书籍。

这个功能其实很简单，因为后台的搜索API接口是类似的：

```
var uri="http://api.rsywx.com/books/list/"+this.search+"/"+this.keyword+"/"+this.page;
```

这个搜索函数的调用中，只要`search`为tag就代表按照tag去搜索。这时的`keyword`就是某一个特定的tag。远程的API会根据传入参数的不同，调整搜索的方向并返回相应的数据集。

Voila!我们又很快地完成了藏书列表页面！

我们最后要完成的是“书评列表”页面。