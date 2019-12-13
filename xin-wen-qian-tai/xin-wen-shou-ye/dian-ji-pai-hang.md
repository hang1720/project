# 点击排行 \|

* 在请求根路由时去数据库查询按点击量排行的10条新闻

```text
@index_blu.route('/')
def index():
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
        "user_info": user.to_dict() if user else None,
        "click_news_list": click_news_list,
    }

    return render_template('news/index.html', data=data)
```

* html 模板渲染

```text
<div class="rank_con fr">
    <div class="rank_title">
        <h3>点击排行</h3>
    </div>
    <ul class="rank_list">
        {% for news in data.click_news_list %}
            <li><span>{{ loop.index }}</span><a href="#">{{ news.title }}</a></li>
        {% endfor %}
    </ul>
</div>
```

> 运行测试，能看到具体效果，但是排行新闻要实现效果是前面前三个新闻有具体的图标显示，如下：

要实现跟原效果一样，可能有多种方式，目前采用使用自定义过滤器的形式对 `span` 标签的 class 指定

* 在 **utils** 目录下创建 `common.py` 文件，并添加以下代码：

```text
def do_index_class(index):
    """自定义过滤器，过滤点击排序html的class"""
    if index == 0:
        return "first"
    elif index == 1:
        return "second"
    elif index == 2:
        return "third"
    else:
        return ""
```

* 在 app 创建的函数里面注册过滤器

```text
from info.utils.common import do_index_class

app.add_template_filter(do_index_class, "index_class")
```

* 在 `index.html` 中使用自定义过滤器

```text
<ul class="rank_list">
    {% for news in data.click_news_list %}
        <li><span class="{{ loop.index0 | indexClass }}">{{ loop.index }}</span><a href="#">{{ news.title }}</a></li>
    {% endfor %}
</ul>
```

> 运行测试

