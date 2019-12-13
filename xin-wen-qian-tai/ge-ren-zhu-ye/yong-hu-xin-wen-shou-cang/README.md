# 用户新闻收藏 \|

## 需求分析 <a id="&#x9700;&#x6C42;&#x5206;&#x6790;"></a>

* 使用分页的形式展示用户所收藏的新闻
* 可以使用 url 的查询字符串传入不同的页数进行模板渲染不同的数据
* 可以支付使用模板显示，也可以使用 ajax 进行请求

## 模板渲染 <a id="&#x6A21;&#x677F;&#x6E32;&#x67D3;"></a>

* 将 `static/news/user_collection.html` 拖动到模板文件夹下的 `news` 文件夹内
* 在 `profile/views.py` 中添加视图函数

```text
@profile_blu.route('/collection')
@user_login_data
def user_collection():
    return render_template('news/user_collection.html')
```

* 修改 `news/user.html` 中我的收藏的链接

```text
<li><a href="/user/collection" target="main_frame">我的收藏</a></li>
```

