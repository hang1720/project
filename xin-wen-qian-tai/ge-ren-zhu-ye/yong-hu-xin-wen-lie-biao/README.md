# 用户新闻列表 \|

## 需求分析 <a id="&#x9700;&#x6C42;&#x5206;&#x6790;"></a>

* 进行到页面之后加载当前用户发布的所有新闻
* 分页展示，以时间倒序排序
* 显示新闻当前的状态，如果未通过审核，需要显示相关原因

## 模板渲染 <a id="&#x6A21;&#x677F;&#x6E32;&#x67D3;"></a>

* 将 `static/news/user_news_list.html` 拖动到模板文件夹下的 `news` 文件夹内
* 在 `profile/views.py` 中添加视图函数

```text
@profile_blu.route('/news_list')
@user_login_data
def news_list():
    return render_template('news/user_news_list.html')
```

* 修改 `news/user.html` 中【新闻列表】的链接

```text
<li><a href="/user/news_list" target="main_frame">新闻列表</a></li>
```

