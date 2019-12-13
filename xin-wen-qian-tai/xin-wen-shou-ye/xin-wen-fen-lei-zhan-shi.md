# 新闻分类展示 \|

* 在请求根路由的时候去查询新闻分类，并默认设置第1个分类选中

```text
@index_blu.route('/')
def index():
    ...
    
    categories = Category.query.all()
    
    categories_dicts = []

    for category in enumerate(categories):
        
        categories_dicts.append(category.to_dict())

    data = {
        "user_info": user.to_dict() if user else None,
        "click_news_list": click_news_list,
        "categories": categories_dicts
    }
    return render_template('news/index.html', data=data)
```

* 前端html数据展示

```text
<ul class="menu fl">
    {% for category in data.categories %}
        <li class="{% if loop.index0 == 0 %}active{% endif %}" data-cid="{{ category.id }}"><a href="javascript:;">{{ category.name }}</a></li>
    {% endfor %}
</ul>
```

> 运行测试

