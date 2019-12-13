# 新闻详情 \|

## 学习目标 <a id="&#x5B66;&#x4E60;&#x76EE;&#x6807;"></a>

* 能够完成新闻首页和新闻详情页的基类模板抽取
* 能够使用装饰器的形式完成用户数据的查询并传递到视图函数中
* 能够查询新闻详情页数据并使用模板语言进行数据的展示
* 能够使用代码判断用户是否收藏某个新闻
* 能够写出新闻收藏和取消收藏的后端代码逻辑
* 能够写出新闻评论的后端逻辑
* 能够写出新闻点赞的后端逻辑
* 能够说出指定新闻的评论列表里面当前用户都点赞了哪些评论的查询逻辑

## 准备工作 <a id="&#x51C6;&#x5907;&#x5DE5;&#x4F5C;"></a>

新闻详情页面的前端页面为 `news/detail.html`，创建新闻详情页的模块`news`，并在该模块下创建视图函数存储的 py 文件 `views.py`

* 创建蓝图

```text
from flask import Blueprint

news_blu = Blueprint("news", __name__, url_prefix='/news')

from . import views
```

* 注册蓝图

```text
def create_app(config_name):
    ...
    
    from info.modules.index import index_blu
    app.register_blueprint(index_blu)

    from info.modules.passport import passport_blu
    app.register_blueprint(passport_blu)

    from info.modules.news import news_blu
    app.register_blueprint(news_blu)
    ...
    return app
``

- 提供新闻详情页访问的视图函数
    - 将新闻详情页界面移动到模板文件夹
    - 视图函数需要接受新闻 id 作为参数，以便后续查询新闻详情数据


```python 
@news_blu.route('/<int:news_id>')
def news_detail(news_id):
    return render_template('news/detail.html')
```

## 完善首页链接 <a id="&#x5B8C;&#x5584;&#x9996;&#x9875;&#x94FE;&#x63A5;"></a>

* 添加首页所有需要点击到新闻详情的链接地址
  * 首页点击排行新闻/news/index.html
  * 首页列表新闻\(/news/index.js\)

```text
<div class="rank_con fr">
    <div class="rank_title">
        <h3>点击排行</h3>
    </div>
    <ul class="rank_list">
        {% for news in data.click_news_list %}
            <li><span class="{{ loop.index0 | index_class }}">{{ loop.index }}</span><a href="/news/{{ news.id }}">{{ news.title }}</a></li>
        {% endfor %}
    </ul>
</div>
```

```text
$.get("/newslist", params, function (resp) {
    data_querying = false
    if (resp) {
        
        total_page = resp.totalPage
        if (cur_page == 1) {
            $(".list_con").html('')
        }
        cur_page += 1
        for (var i=0;i<resp.newsList.length;i++) {
            var news = resp.newsList[i]
            var content = '<li>'
            content += '<a href="/news/'+ news.id +'" class="news_pic fl"><img src="' + news.index_image_url + '?imageView2/1/w/170/h/170"></a>'
            content += '<a href="/news/'+ news.id +'" class="news_title fl">' + news.title + '</a>'
            content += '<a href="/news/'+ news.id +'" class="news_detail fl">' + news.digest + '</a>'
            content += '<div class="author_info fl">'
            content += '<div class="source fl">来源：' + news.source + '</div>'
            content += '<div class="time fl">' + news.create_time + '</div>'
            content += '</div>'
            content += '</li>'
            $(".list_con").append(content)
        }
    }
})
```

> 运行测试

