# 详情页新闻数据展示 \|

## 新闻内容数据 <a id="&#x65B0;&#x95FB;&#x5185;&#x5BB9;&#x6570;&#x636E;"></a>

* 查询新闻数据并返回

```text
@news_blu.route('/<int:news_id>')
@user_login_data
def news_detail(news_id):
    try:
        news = News.query.get(news_id)
    except Exception as e:
        current_app.logger.error(e)
        abort(404)

    if not news:
        
        abort(404)

    news.clicks += 1
    data = {
        "news": news.to_dict(),
        "user_info": g.user.to_dict() if g.user else None,
    }
    return render_template('news/detail.html', data=data)
```

> 数据未找到页面\(404\)暂不实现，后续再实现

* 前端模板实现

```text
<div class="detail_con fl">
    <h3>{{ data.news.title }}</h3>
    <div class="detail_about clearfix">
        <span class="time_souce fl">{{ data.news.create_time }} 来源: {{ data.news.source }}</span>
        <span class="comment fr">{{ data.news.comments_count }}</span>
    </div>
    {{ data.news.content | safe }}
    ...
    {% if data.user_info %}
        <form action="" class="comment_form" data-newsid="{{ data.news.id }}>
            <div class="person_pic">
                <img src="{% if data.user_info.avatar_url %}
                    {{ data.user_info.avatar_url }}
                {% else %}
                    ../../static/news/images/person01.png
                {% endif %}" alt="用户图标">
            </div>
            <textarea placeholder="请发表您的评论" class="comment_input"></textarea>
            <input type="submit" name="" value="评 论" class="comment_sub">
        </form>
    {% else %}
        <div class="comment_form_logout">
            登录发表你的评论
        </div>
    {% endif %}

    <div class="comment_count">
        {{ data.news.comments_count }}条评论
    </div>
    ...
</div>
```

> 运行测试

## 排行新闻数据 <a id="&#x6392;&#x884C;&#x65B0;&#x95FB;&#x6570;&#x636E;"></a>

* 查询新闻点击量数据，并返回

```text
@news_blu.route('/<int:news_id>')
@user_login_data
def news_detail(news_id):
    ...
    
    news_list = None
    try:
        news_list = News.query.order_by(News.clicks.desc()).limit(constants.CLICK_RANK_MAX_NEWS)
    except Exception as e:
        current_app.logger.error(e)

    click_news_list = []
    for news in news_list if news_list else []:
        click_news_list.append(news.to_basic_dict())
    data = {
        "news": news.to_dict(),
        "click_news_list": click_news_list,
        "user_info": g.user.to_dict() if g.user else None,
    }
    return render_template('news/detail.html', data=data)\
```

> 运行测试 新闻作者功能暂时不实现，后续再实现

