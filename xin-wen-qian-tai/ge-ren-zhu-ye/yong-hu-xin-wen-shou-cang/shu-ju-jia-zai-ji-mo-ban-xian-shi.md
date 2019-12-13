# 数据加载及模板显示 \|

## 收藏数据加载 <a id="&#x6536;&#x85CF;&#x6570;&#x636E;&#x52A0;&#x8F7D;"></a>

* 对用户收藏的新闻进行分页返回，默认返回第1页
  * 返回的数据中要告诉前端 总页数、当前页数

```text
@profile_blu.route('/collection')
@user_login_data
def user_collection():

    
    p = request.args.get("p", 1)
    try:
        p = int(p)
    except Exception as e:
        current_app.logger.error(e)
        p = 1

    user = g.user
    collections = []
    current_page = 1
    total_page = 1
    try:
        
        paginate = user.collection_news.paginate(p, constants.USER_COLLECTION_MAX_NEWS, False)
        
        collections = paginate.items
        
        current_page = paginate.page
        
        total_page = paginate.pages
    except Exception as e:
        current_app.logger.error(e)

    
    collection_dict_li = []
    for news in collections:
        collection_dict_li.append(news.to_basic_dict())

    data = {"total_page": total_page, "current_page": current_page, "collections": collection_dict_li}
    return render_template('news/user_collection.html', data=data)
```

## 前端模板展示 <a id="&#x524D;&#x7AEF;&#x6A21;&#x677F;&#x5C55;&#x793A;"></a>

```text
<div class="my_collect">
    <h3>我的收藏</h3>
    <ul class="article_list">
        {% for news in data.collections %}
            <li><a href="/news/{{ news.id }}" target="_blank">{{ news.title }}</a><span>{{ news.create_time }}</span></li>
        {% endfor %}
    </ul>

    <div id="pagination" class="page"></div>
    <script>
        $(function() {
            $("#pagination").pagination({
                currentPage: {{ data.current_page }},
                totalPage: {{ data.total_page }},
                callback: function(current) {
                    window.location.href = "/user/collection?p=" + current
                }
            });
        });
    </script>
</div>
```

> 运行测试

