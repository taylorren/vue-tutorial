# 书评列表

“书评列表”与“书籍列表”类似，都有一个分页显示（并由另外一个组件实现），只是“书评列表”没有搜索功能，而且有两个循环来显示所有与一本书籍相关的评论（因为对某本特定的书，可能有多于一篇的评论）。

## 第一个循环：列出所有被评论过的书

这个过程很简单。在`created`中获取相应的API即可：

```
created: function() {
    var uri = "http://api.rsywx.com/reading/listHeadline/" + this.page;
    
    fetch(uri)
      .then(res => {
        return res.json();
      })
      .then(json => {
        var headlines= json.out[0];
        this.count = json.out[1];
        this.pages=Math.ceil(this.count/5);

        for(var h in headlines) {
          
          var book=headlines[h].book;
          headlines[h].book.cover="http://api.rsywx.com/book/cover/"+book.bookid+"/"+book.title+"/"+book.author+"/200";
        }

        this.headlines=headlines;
      });
  }
```

## 第二个循环：列出每本书的评论

循环显示每本书的评论也很简单。我们创建一个新的子组件`ReviewList`。它的核心代码是：

```
<template>
<tr>
                  <td colspan="4">
                    <table class="table table-hover table-striped">
                      <tr>
                        <th class="col-md-1"><strong>评论编号</strong></th>
                        <th class="col-md-9"><strong>评论标题</strong></th>
                        <th class="col-md-2 "><strong>评论日期</strong></th>
                      </tr>
                      <tr v-for="review in reviews" :key="review.id">
                        <td>{{review.id}}</td>
                        <td><a :href="review.URI">{{review.title}}</a></td>
                        <td>{{review.datein}}</td>
                      </tr>
                      
                    </table>
                  </td>
                </tr>
</template>

<script>
export default {
    name: "ReadingList",
    props: [
        'bookid',
    ],
    data: function() {
      return {
        reviews: [],
      }
    },
    methods: {
      getReviews(id) {
        var uri="http://api.rsywx.com/reading/relatedReview/" + id;
        fetch(uri)
            .then(res => {
                return res.json();
            })
            .then(json => {
                this.reviews=json.out;
            })
      }
    },
    created: function() {
      this.getReviews(this.bookid);
    }
}
</script>
```

## 将ReviewList组件嵌入ReadingList：

在`ReadingList.js`中，我们先是对所有有评论的书籍进行循环，然后在需要显示相关联书评的地方用这样的代码：

```
<ReviewList :bookid="headline.book.bookid" :key="headline.hid"/>
```

很好，很强大。这个组件和页面的创建几乎只是我们已经学会了的东西的再练习。

![](http://rsywx.com/lib/exe/fetch.php/react:11-01.png)