# 点赞计数功能 \|

* 实现评论如果有点赞，就显示点赞的条数，并使用自定义属性记录当前条数

```text
<a href="javascript:;" class="comment_up
{% if comment.is_like %}
    has_comment_up
{% endif %} fr" 
   data-commentid="{{ comment.id }}" 
   data-likecount="{{ comment.like_count }}"
   data-newsid="{{ data.news.id }}">
{% if comment.like_count > 0 %}
    {{ comment.like_count }}
{% else %}
    赞
{% endif %}</a>
```

* 在点赞/取消点赞操作完成之后，更新条数

```text
$.ajax({
    ...
    success: function (resp) {
        if (resp.errno == "0") {
            var like_count = $this.attr('data-likecount')
            
            if (action == "add") {
                like_count = parseInt(like_count) + 1
                
                $this.addClass('has_comment_up')
            }else {
                like_count = parseInt(like_count) - 1
                $this.removeClass('has_comment_up')
            }
            
            $this.attr('data-likecount', like_count)
            if (like_count == 0) {
                $this.html("赞")
            }else {
                $this.html(like_count)
            }
        }else if (resp.errno == "4101"){
            $('.login_form_con').show();
        }else {
            alert(resp.errmsg)
        }
    }
})
```

> 运行测试

