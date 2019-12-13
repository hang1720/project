# 新闻评论列表 \|

* 在新闻详情的视图函数中，添加查询当前新闻评论的逻辑

```text
@news_blu.route('/<int:news_id>')
@user_login_data
def news_detail(news_id):
    ...
    
    comments = []
    try:
        comments = Comment.query.filter(Comment.news_id == news_id).order_by(Comment.create_time.desc()).all()
    except Exception as e:
        current_app.logger.error(e)
    comment_list = []
    for item in comments:
        comment_dict = item.to_dict()
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

* 在 `detail.html` 中，使用模板语言将评论渲染出来

```text
{% for comment in data.comments %}
    <div class="comment_list">
        <div class="person_pic fl">
            <img src="{% if comment.user.avatar_url %}
                {{ comment.user.avatar_url }}
            {% else %}
                ../../static/news/images/person01.png
            {% endif %}" alt="用户图标">
        </div>
        <div class="user_name fl">{{ comment.user.nick_name }}</div>
        <div class="comment_text fl">{{ comment.content }}</div>
        {% if comment.parent %}
            <div class="reply_text_con fl">
                <div class="user_name2">{{ comment.parent.user.nick_name }}</div>
                <div class="reply_text">
                    {{ comment.parent.content }}
                </div>
            </div>
        {% endif %}
        <div class="comment_time fl">{{ comment.create_time }}</div>
        <a href="javascript:;" class="comment_up fr" data-commentid="{{ comment.id }}" data-newsid="{{ comment.news_id }}">赞</a>
        <a href="javascript:;" class="comment_reply fr">回复</a>
        <form class="reply_form fl" data-commentid="{{ comment.id }}" data-newsid="{{ data.news.id }}">
            <textarea class="reply_input"></textarea>
            <input type="button" value="回复" class="reply_sub fr">
            <input type="reset" name="" value="取消" class="reply_cancel fr">
        </form>
    </div>
{% endfor %}
```

> 运行测试，注：在 `reply_form` 表单中添加了自定义属性，以便在回复该评论的时候获取到评论的id

