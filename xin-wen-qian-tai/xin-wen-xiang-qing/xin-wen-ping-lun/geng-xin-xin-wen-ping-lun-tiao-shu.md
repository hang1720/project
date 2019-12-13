# 更新新闻评论条数 \|

* 思路：获取新闻评论 div\(comment\_list\) 的数量，在 detail.js 里面添加以下函数

```text
// 更新评论条数
function updateCommentCount() {
    var length = $(".comment_list").length
    $(".comment_count").html(length + "条评论")
}
```

> 在评论完毕之后调用此方法

