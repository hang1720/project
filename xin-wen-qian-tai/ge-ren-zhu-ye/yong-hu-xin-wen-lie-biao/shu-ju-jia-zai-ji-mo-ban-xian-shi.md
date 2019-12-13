# 数据加载及模板显示 \|

## 新闻列表数据加载 <a id="&#x65B0;&#x95FB;&#x5217;&#x8868;&#x6570;&#x636E;&#x52A0;&#x8F7D;"></a>

* 对用户发布的新闻进行分页返回，默认返回第1页
  * 返回的数据中要告诉前端 总页数、当前页数

```text
@profile_blu.route('/news_list')
@user_login_data
def news_list():
    
    p = request.args.get("p", 1)
    try:
        p = int(p)
    except Exception as e:
        current_app.logger.error(e)
        p = 1

    user = g.user
    news_li = []
    current_page = 1
    total_page = 1
    try:
        paginate = News.query.filter(News.user_id == user.id).paginate(p, constants.USER_COLLECTION_MAX_NEWS, False)
        
        news_li = paginate.items
        
        current_page = paginate.page
        
        total_page = paginate.pages
    except Exception as e:
        current_app.logger.error(e)

    news_dict_li = []

    for news_item in news_li:
        news_dict_li.append(news_item.to_review_dict())
    data = {"news_list": news_dict_li, "total_page": total_page, "current_page": current_page}
    return render_template('news/user_news_list.html', data=data)
```

## 前端模板展示 <a id="&#x524D;&#x7AEF;&#x6A21;&#x677F;&#x5C55;&#x793A;"></a>

```text
<div class="news_list">
    <h3>新闻列表</h3>
    <ul class="article_list">
        {% for news in data.news_list %}
            <li>
            {% if news.status == 0 %}
                {# 审核通过 #}
                <a href="/news/{{ news.id }}">{{ news.title }}</a><em class="pass">已通过</em><span>{{ news.create_time }}</span>
            {% elif news.status == 1 %}
                {# 审核中 #}
                <a href="javascript:;">{{ news.title }}</a><em class="review">审核中</em><span>{{ news.create_time }}</span>
            {% else %}
                {# 审核不通过 #}
                <a href="javascript:;">{{ news.title }}</a><em class="nopass">未通过</em><span>{{ news.create_time }}</span>
                <b>未通过原因：{{ news.reason }}</b>
            {% endif %}
            </li>
        {% endfor %}
    </ul>
    <div id="pagination" class="page"></div>
    <script>
        $(function(){
            $("#pagination").pagination({
                currentPage: {{ data.current_page }},
                totalPage: {{ data.total_page }},
                callback: function(current) {
                    window.location.href = '/user/news_list?p=' + current
                }
            });
        });
    </script>
</div>
```

> 运行测试

