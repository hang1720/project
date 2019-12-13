# 是否点赞数据返回 \|

* 在新闻详情视图函数中，如果用户已登录，返回的评论要记录当前用户是否点赞，代码如下：

```text
@news_blu.route('/<int:news_id>')
@user_login_data
def news_detail(news_id):
    ...
    
    comments = None
    try:
        comments = Comment.query.filter(Comment.news_id == news_id).order_by(Comment.create_time.desc()).all()
    except Exception as e:
        current_app.logger.error(e)

    comment_like_ids = []
    if g.user:
        
        try:
            comment_ids = [comment.id for comment in comments]
            if len(comment_ids) > 0:
                
                comment_likes = CommentLike.query.filter(CommentLike.comment_id.in_(comment_ids),
                                                         CommentLike.user_id == g.user.id).all()
                
                comment_like_ids = [comment_like.comment_id for comment_like in comment_likes]
        except Exception as e:
            current_app.logger.error(e)

    comment_list = []
    for item in comments if comments else []:
        comment_dict = item.to_dict()
        comment_dict["is_like"] = False
        
        if g.user and item.id in comment_like_ids:
                comment_dict["is_like"] = True
        comment_list.append(comment_dict)

    is_collected = False
    
    if g.user:
        if news in g.user.collection_news:
            is_collected = True
    data = {
        "news": news.to_dict(),
        "click_news_list": click_news_list,
        "is_collected": is_collected,
        "user_info": g.user.to_dict() if g.user else None,
        "comments": comment_list
    }
    return render_template('news/detail.html', data=data)
```

* 前端界面展示

```text
<a href="javascript:;" class="comment_up {% if comment.is_like %}has_comment_up{% endif %} fr" data-commentid="{{ comment.id }}" data-newsid="{{ data.news.id }}">赞</a>
```

