# 回复评论前端实现 \|

* 在渲染模板时，给回复评论表单添加自定义属性，以记录当前回复的是哪一个评论

  ```text
  <form class="reply_form fl" data-commentid="" data-newsid="">
    <textarea class="reply_input"></textarea>
    <input type="button" value="回复" class="reply_sub fr">
    <input type="reset" name="" value="取消" class="reply_cancel fr">
  </form>
  ```

```text

if(sHandler.indexOf('reply_sub')>=0)
{
    var $this = $(this)
    var news_id = $this.parent().attr('data-newsid')
    var parent_id = $this.parent().attr('data-commentid')
    var comment = $this.prev().val()

    if (!comment) {
        alert('请输入评论内容')
        return
    }
    var params = {
        "news_id": news_id, 
        "comment": comment, 
        "parent_id": parent_id
    }
    $.ajax({
        url: "/news/news_comment",
        type: "post",
        contentType: "application/json",
        headers: {
            "X-CSRFToken": getCookie("csrf_token")
        },
        data: JSON.stringify(params),
        success: function (resp) {
            if (resp.errno == "0") {
                var comment = resp.data
                
                var comment_html = ""
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
                comment_html += '<div class="reply_text_con fl">'
                comment_html += '<div class="user_name2">' + comment.parent.user.nick_name + '</div>'
                comment_html += '<div class="reply_text">'
                comment_html += comment.parent.content
                comment_html += '</div>'
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
                
                $this.prev().val('')
                
                $this.parent().hide()
            }else {
                alert(resp.errmsg)
            }
        }
    })
}
```

