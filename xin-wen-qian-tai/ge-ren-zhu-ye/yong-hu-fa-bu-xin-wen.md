# 用户发布新闻 \|

## 需求分析 <a id="&#x9700;&#x6C42;&#x5206;&#x6790;"></a>

* 用户可以在个人中心发布新闻
* 发布完毕新闻需要通过审核才能显示
* 用户发布完毕新闻跳转到用户新闻列表
* 后台管理员对新闻进行审核
* 如果某条新闻是用户发布的，那么在新闻详情页展示该用户的个人资料
* 需要更新新闻查询条件，添加 status == 0\(已审核通过\)

## 模板渲染 <a id="&#x6A21;&#x677F;&#x6E32;&#x67D3;"></a>

* 将 `static/news/user_news_release.html` 拖动到模板文件夹下的 `news` 文件夹内
* 在 `profile/views.py` 中添加视图函数

```text
@profile_blu.route('/news_release')
@user_login_data
def news_release():

    categories = []
    try:
        
        categories = Category.query.all()
    except Exception as e:
        current_app.logger.error(e)

    
    categories_dicts = []

    for category in categories:
        
        cate_dict = category.to_dict()
        
        categories_dicts.append(cate_dict)

    
    categories_dicts.pop(0)
    
    return render_template('news/user_news_release.html', data={"categories": categories_dicts})
```

* 在 `user_news_release.html` 中修改相关模板语言

```text
<form class="release_form">
    <h3>新闻发布</h3>
    <div class="form-group"><label>新闻标题：</label><input type="text" class="input_txt2"></div>
    <div class="form-group">
        <label>新闻分类：</label>
        <select class="sel_opt">
            {% for category in data.categories %}
                <option value="{{ category.id }}">{{ category.name }}</option>
            {% endfor %}
        </select>
    </div>
    ...
</form>
```

* 修改 `news/user.html` 中新闻发布的链接

```text
<li><a href="/user/news_release" target="main_frame">新闻发布</a></li>
```

