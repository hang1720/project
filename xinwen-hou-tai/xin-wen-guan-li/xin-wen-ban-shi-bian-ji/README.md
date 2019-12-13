# 新闻版式编辑 \|

## 需求分析 <a id="&#x9700;&#x6C42;&#x5206;&#x6790;"></a>

* 以分页的形式按新闻创建时间倒序展示出新闻数据
* 可以使用关键这这对新闻标题进行搜索
* 点击编辑进入编辑详情页面

## 实现准备 <a id="&#x5B9E;&#x73B0;&#x51C6;&#x5907;"></a>

* 将 `static/admin/` 目录下的 `news_edit.html` 以及 `news_edit_detail.html` 拖到 `templates/admin/` 目录下

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `admin/views.py` 文件中添加视图函数以返回新闻列表

```text
@admin_blu.route('/news_edit')
def news_edit():
    """返回新闻列表"""

    page = request.args.get("p", 1)
    keywords = request.args.get("keywords", "")
    try:
        page = int(page)
    except Exception as e:
        current_app.logger.error(e)
        page = 1

    news_list = []
    current_page = 1
    total_page = 1

    try:
        filters = []
        
        if keywords:
            
            filters.append(News.title.contains(keywords))

        
        paginate = News.query.filter(*filters) \
            .order_by(News.create_time.desc()) \
            .paginate(page, constants.ADMIN_NEWS_PAGE_MAX_COUNT, False)

        news_list = paginate.items
        current_page = paginate.page
        total_page = paginate.pages
    except Exception as e:
        current_app.logger.error(e)

    news_dict_list = []
    for news in news_list:
        news_dict_list.append(news.to_basic_dict())

    context = {"total_page": total_page, "current_page": current_page, "news_list": news_dict_list}

    return render_template('admin/news_edit.html', data=context)
```

* 修改 `templates/admin/index.html` 中【新闻版式编辑】链接

```text
<li><a class="icon032" href="/admin/news_edit" target="main_frame">新闻版式编辑</a></li>
```

* 前端页面界面数据渲染

```text
<div class="pannel">            
    <table class="common_table">
        <tr>
            <th width="5%">id</th>
            <th width="60%">标题</th>
            <th width="10%">点击量</th>
            <th width="15%">发布时间</th>
            <th width="10%">管理操作</th>
        </tr>
        {% for news in data.news_list %}
            <tr>
                <td>{{ news.id }}</td>
                <td class="tleft"><a href="/news/{{ news.id }}" target="_blank">{{ news.title }}</a></td>
                <td>{{ news.clicks }}</td>
                <td>{{ news.create_time }}</td>
                <td>
                    <a href="#" class="edit">编辑</a>
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
                window.location = '/admin/news_edit?p=' + current
            }
        });
    });
</script>
```

