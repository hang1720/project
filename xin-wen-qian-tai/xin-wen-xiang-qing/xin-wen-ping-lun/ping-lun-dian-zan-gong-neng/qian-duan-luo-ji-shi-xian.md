# 前端逻辑实现 \|

* 给点赞的 a 标签添加自定义属性，以记录当前点赞的新闻id和评论id，供点赞/取消点赞请求使用

```text
<a href="javascript:;" class="comment_up fr" data-commentid="{{ comment.id }}" data-newsid="{{ data.news.id }}">赞</a>
```

* 实现点赞逻辑

```text
if(sHandler.indexOf('comment_up')>=0)
{
    var $this = $(this);
    var action = "add"
    if(sHandler.indexOf('has_comment_up')>=0)
    {
        
        action = "remove"
    }

    var comment_id = $(this).attr("data-commentid")
    var news_id = $(this).attr("data-newsid")
    var params = {
        "comment_id": comment_id,
        "action": action,
        "news_id": news_id
    }

    $.ajax({
        url: "/news/comment_like",
        type: "post",
        contentType: "application/json",
        headers: {
            "X-CSRFToken": getCookie("csrf_token")
        },
        data: JSON.stringify(params),
        success: function (resp) {
            if (resp.errno == "0") {
                
                if (action == "add") {
                    
                    $this.addClass('has_comment_up')
                }else {
                    $this.removeClass('has_comment_up')
                }
            }else if (resp.errno == "4101"){
                $('.login_form_con').show();
            }else {
                alert(resp.errmsg)
            }
        }
    })
}
```

