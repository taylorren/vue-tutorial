# 摘要组件

这一章中我们要实现“摘要组件”，分别给出藏书、读书、博客、维客的摘要信息。前三个牵涉到动态内容，最后一个只有静态内容。

与[前一章](https://rsywx.gitbook.io/vue/huan-deng-pian-zu-jian)中的做法类似，我们也进一步将该组件分解为四个小组件：

  * `BookSummary`：显示藏书的摘要信息。
  * `ReadingSummary`：显示读书的摘要信息。
  * `BlogSummary`：显示博客的摘要信息。
  * 直接用`<div>`显示维客信息和其他杂项信息。

于是`/src/components/Intro.vue`会被改写为：

```
<template>
<div class="widewrapper">
  <div class="container">
    <div class="row features">
      <BookSummary />
      <ReadingSummary />
      <BlogSummary />
      <div class="col-md-3 feature">
        <h3><a href="http://www.rsywx.com"><i class="glyphicons notes_2"></i>维客</a></h3>
          <p>这里是我整理的一些资源，主要是电子书和我最喜爱的<strong><a href="/misc/lakers/2017">湖人队的赛程</a></strong>。</p>
          <p>还可以和我<strong><a href="/contact">取得联系</a></strong>。</p>
      </div>
    </div>
  </div>
</div>    
</template>

<script>
import BookSummary from '@/components/BookSummary';
import ReadingSummary from '@/components/ReadingSummary';
import BlogSummary from '@/components/BlogSummary';

export default {
  name: "Intro",
  components: {
    BookSummary,
    ReadingSummary,
    BlogSummary,
  }
};
</script>
```

下面我们以`BookSummary`为例，简要说明一下编程要点。

```
<template>
<div class="col-md-3 feature">
  <h3><a href="/books/list/title/all/1"><i class="glyphicons book"></i>藏书</a></h3>
  <p>截止{{today.getFullYear()}}年{{String(today.getMonth()+1).padStart(2,'0')}}月{{String(today.getDate()).padStart(2,'0')}}日，任氏有无轩藏书{{formatter.format(summary.bc)}}本。约{{formatter.format(summary.wc)}}千字，计{{formatter.format(summary.pc)}}页。</p>
  <p>
      最近（{{last.purchdate}}）收藏/整理的书籍是<strong>{{last.author}}</strong>的<strong><router-link :to="{name: 'BookDetail', params: {id: last.bookid} }">《{{last.title}}》</router-link></strong>
  </p>
</div>  
</template>

<script>
export default {
  name: "BookSummary",
  data() {
    return {
      summary: [],
      last: [],
      today: new Date(),
      formatter: new Intl.NumberFormat('en-US', {minimumFractionDigits: 0}),
    };
  },
  created: function() {
    this.getBookSummary();
  },
  methods: {
    getBookSummary() {
      var uri='http://api.rsywx.com/book/summary';
      fetch(uri)
        .then(res => {
          return res.json();
        })
        .then(json => {
          this.summary=json.out.summary[0];
          this.last=json.out.last[0];
        })
    }
  }
};
</script>
```

基本上，这都是获取数据、格式数据然后设置状态的过程。

在`<template>...</template>`中，我们主要也就是将模板的内容复制过来，对需要动态内容的地方进行替换即可：

其他几个子组件的编写与此类似，不再赘述。

现在我们的主页面会是这样的：

![](http://rsywx.com/lib/exe/fetch.php/vue:06-01.png)

在下一章，我们讨论“历史上的今天”这个组件的编写。