# 用户密码修改 \|

## 需求分析 <a id="&#x9700;&#x6C42;&#x5206;&#x6790;"></a>

* 使用原密码和新密码进行密码修改

## 模板渲染 <a id="&#x6A21;&#x677F;&#x6E32;&#x67D3;"></a>

* 将 `static/news/user_pass_info.html` 拖动到模板文件夹下的 `news` 文件夹内
* 在 `profile/views.py` 中添加视图函数

```text
@profile_blu.route('/pass_info', methods=["GET", "POST"])
@user_login_data
def pass_info():
    return render_template('news/user_pass_info.html')
```

* 修改 `news/user.html` 中密码修改的链接

```text
<li><a href="/user/pass_info" target="main_frame">密码修改</a></li>
```

