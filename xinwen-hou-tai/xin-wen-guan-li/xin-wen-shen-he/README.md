# 新闻审核 \|

## 需求分析 <a id="&#x9700;&#x6C42;&#x5206;&#x6790;"></a>

* 以分页的形式按新闻创建时间倒序展示出待审核的新闻数据
* 可以使用关键这这对新闻标题进行搜索
* 点击审核进入审核详情页面\(对新闻只能查看不能编辑\)
* 审核不通过需要写明不通过原因

## 实现准备 <a id="&#x5B9E;&#x73B0;&#x51C6;&#x5907;"></a>

* 将 `static/admin/` 目录下的 `news_review.html` 以及 `news_review_detail.html` 拖到 `templates/admin/` 目录下

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `admin/views.py` 文件中添加视图函数以返回待审核新闻

```text
@admin_blu.route('/news_review')
def news_review():
    """返回待审核新闻列表"""

    page = request.args.get("p", 1)
    try:
        page = int(page)
    except Exception as e:
        current_app.logger.error(e)
        page = 1

    news_list = []
    current_page = 1
    total_page = 1

    try:
        paginate = News.query.filter(News.status != 0) \
            .order_by(News.create_time.desc()) \
            .paginate(page, constants.ADMIN_NEWS_PAGE_MAX_COUNT, False)

        news_list = paginate.items
        current_page = paginate.page
        total_page = paginate.pages
    except Exception as e:
        current_app.logger.error(e)

    news_dict_list = []
    for news in news_list:
        news_dict_list.append(news.to_review_dict())

    context = {"total_page": total_page, "current_page": current_page, "news_list": news_dict_list}

    return render_template('admin/news_review.html', data=context)
```

* 修改 `templates/admin/index.html` 中【新闻审核】链接

```text
<li><a class="icon031" href="/admin/news_review" target="main_frame">新闻审核</a></li>
```

* 前端页面界面数据渲染

```text
<div class="pannel">
    <table class="common_table">
        <tr>
            <th width="3%">id</th>
            <th width="70%">标题</th>
            <th width="15%">发布时间</th>
            <th width="5%">状态</th>
            <th width="8%">管理操作</th>
        </tr>
        {% for news in data.news_list %}
            <tr>
                <td>{{ news.id }}</td>
                <td class="tleft">{{ news.title }}</td>
                <td>{{ news.create_time }}</td>
                <td>{% if news.status == 0 %}
                    已通过
                    {% elif news.status == -1 %}
                    未通过
                    {% else %}
                    未审核
                {% endif %}</td>
                <td>
                    <a href="#" class="review">审核</a>
                </td>
            </tr>
        {% endfor %}
    </table>
</div>

...

<script>
    $(function() {
        $("#pagination").pagination({
            currentPage: {{ data.current_page }},
            totalPage: {{ data.total_page }},
            callback: function(current) {
                window.location = '/admin/news_review?p=' + current
            }
        });
    });
</script>
```

> 运行测试，能够看到待审核的新闻数据

* 添加新闻标题关键字搜索，修改后台代码为：

```text
@admin_blu.route('/news_review')
def news_review():
    """返回待审核新闻列表"""
    page = request.args.get("p", 1)
    keywords = request.args.get("keywords", "")
    ...
    try:
        filters = [News.status != 0]
        
        if keywords:
            
            filters.append(News.title.contains(keywords))
        
        paginate = News.query.filter(*filters) \
            .order_by(News.create_time.desc()) \
            .paginate(page, constants.ADMIN_NEWS_PAGE_MAX_COUNT, False)
        ...
    except Exception as e:
        current_app.logger.error(e)
    ...
    return render_template('admin/news_review.html', data=context)
```

* 添加前端 Form 表单中 input 的 name 属性

```text
<form class="news_filter_form">
    <input name="keywords" type="text" placeholder="请输入关键字" class="input_txt">
    <input type="submit" value="搜 索" class="input_sub">
</form>
```

> 运行测试

