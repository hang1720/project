# 详情页用户信息 \|

## 需求 <a id="&#x9700;&#x6C42;"></a>

* 如果某条新闻是由用户发布的，那以需要在详情页显示用户的相关信息
* 并且可以点击 关注/取消关注 对该新闻用户信息进行关注
* 如果当前用户已登录，打开某条用户的新闻，需要直接显示是否关注过该用户

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `news/detail.html` 中，填写相关模板数据

```text
{% block authorBlock %}
    {% if data.news.author %}
        <div class="author_card">
            <a href="#" class="author_pic"><img src="{% if data.news.author.avatar_url %}
            {{ data.news.author.avatar_url }}
            {% else %}
            ../../static/news/images/user_pic.png
            {% endif %}" alt="author_pic"></a>
            <a href="#" class="author_name">{{ data.news.author.nick_name }}</a>
            <div class="author_resume">签名：{{ data.news.author.signature }}</div>
            <div class="writings"><span>总篇数</span><b>{{ data.news.author.news_count }}</b></div>
            <div class="follows"><span>粉丝</span><b>{{ data.news.author.followers_count }}</b></div>
            <a href="javascript:;" class="focus fr" data-userid="{{ data.news.author.id }}">关注</a>
            <a href="javascript:;" class="focused fr" data-userid="{{ data.news.author.id }}"><span class="out">已关注</span><span class="over">取消关注</span></a>
        </div>
    {% endif %}
{% endblock %}
```

> 去发布一条新闻，并把新闻的 status 改成 0\(表示审核通过\)，运行测试

* 在返回新闻详情的视图函数的返回值中，添加是否关注当前用户的值

```text
news_blu.route('/<int:news_id>')
@user_login_data
def news_detail(news_id):

    ...

    
    is_followed = False
    
    is_collected = False
    if g.user:
        if news in g.user.collection_news:
            is_collected = True
        if news.user.followers.filter(User.id == g.user.id).count() > 0:
            is_followed = True

    data = {
        "news": news.to_dict(),
        "click_news_list": click_news_list,
        "is_collected": is_collected,
        "user_info": g.user.to_dict() if g.user else None,
        "comments": comment_list,
        "is_followed": is_followed
    }
    return render_template('news/detail.html', data=data)
```

* 添加 `detail.html` 中 `关注` 和 `取消关注` 的显示和隐藏逻辑

```text
<a href="javascript:;" class="focus fr" data-userid="{{ data.news.author.id }}" style="display: {% if data.is_followed %}none{% else %}block{% endif %}">关注</a>
<a href="javascript:;" class="focused fr" data-userid="{{ data.news.author.id }}" style="display: {% if data.is_followed %}block{% else %}none{% endif %}"><span class="out">已关注</span><span class="over">取消关注</span></a>
```

