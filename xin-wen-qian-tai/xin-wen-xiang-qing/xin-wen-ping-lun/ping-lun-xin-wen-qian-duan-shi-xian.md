# 评论新闻前端实现 \|

* 在 `news/detail.html` 中，评论提交的表单里面添加属性，用于记录当前新闻id
  * 在便在提交的时候可以取到当前新闻id

```text
<form action="" class="comment_form" data-newsid="{{ data.news.id }}">
    ...
</form>
```

* 在 `detail.js` 文件中，实现评论提交的代码

```text

$(".comment_form").submit(function (e) {
    e.preventDefault();
    var news_id = $(this).attr('data-newsid')
    var news_comment = $(".comment_input").val();

    if (!news_comment) {
        alert('请输入评论内容');
        return
    }
    var params = {
        "news_id": news_id,
        "comment": news_comment
    };
    $.ajax({
        url: "/news/news_comment",
        type: "post",
        contentType: "application/json",
        headers: {
            "X-CSRFToken": getCookie("csrf_token")
        },
        data: JSON.stringify(params),
        success: function (resp) {
            if (resp.errno == '0') {
                var comment = resp.data
                
                var comment_html = ''
                comment_html += '<div class="comment_list">'
                comment_html += '<div class="person_pic fl">'
                if (comment.user.avatar_url) {
                    comment_html += '<img src="' + comment.user.avatar_url + '" alt="用户图标">'
                }else {
                    comment_html += '<img src="../../static/news/images/person01.png" alt="用户图标">'
                }
                comment_html += '</div>'
                comment_html += '<div class="user_name fl">' + comment.user.nick_name + '</div>'
                comment_html += '<div class="comment_text fl">'
                comment_html += comment.content
                comment_html += '</div>'
                comment_html += '<div class="comment_time fl">' + comment.create_time + '</div>'

                comment_html += '<a href="javascript:;" class="comment_up fr" data-commentid="' + comment.id + '" data-newsid="' + comment.news_id + '">赞</a>'
                comment_html += '<a href="javascript:;" class="comment_reply fr">回复</a>'
                comment_html += '<form class="reply_form fl" data-commentid="' + comment.id + '" data-newsid="' + news_id + '">'
                comment_html += '<textarea class="reply_input"></textarea>'
                comment_html += '<input type="button" value="回复" class="reply_sub fr">'
                comment_html += '<input type="reset" name="" value="取消" class="reply_cancel fr">'
                comment_html += '</form>'

                comment_html += '</div>'
                
                $(".comment_list_con").prepend(comment_html)
                
                $('.comment_sub').blur();
                
                $(".comment_input").val("")
            }else {
                alert(resp.errmsg)
            }
        }
    })
})
```

> 运行测试

