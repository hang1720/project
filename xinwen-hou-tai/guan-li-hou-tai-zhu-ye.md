# 管理后台主页 \|

## 需求 <a id="&#x9700;&#x6C42;"></a>

* 为后台主页提供专门的视图函数
* 需要带入当前管理员用户相关信息以便在界面进行展示

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `modules/admin/views.py` 文件中添加视图函数

```text
@admin_blu.route('/index')
@user_login_data
def admin_index():
    user = g.user
    return render_template('admin/index.html', user=user.to_dict())
```

* `admin/index.html` 模板内容填充

```text

<div class="user_info">
    <img src="../../static/admin/images/person.png" alt="{{ user.nick_name }}">
    <p>欢迎你 <em>{{ user.nick_name }}</em></p>
</div>
```

> 访问测试

* 完善登录视图函数跳转界面逻辑

```text
@admin_blu.route('/login', methods=["GET", "POST"])
def admin_login():
    ...
    
    return redirect(url_for('admin.admin_index'))
```

* 完善如果已登录的管理员用户访问登录页面直接跳转到主页逻辑

```text
def admin_login():
    if request.method == "GET":
        
        user_id = session.get("user_id", None)
        is_admin = session.get("is_admin", False)
        
        if user_id and is_admin:
            return redirect(url_for('admin.admin_index'))
        return render_template('admin/login.html')
    ...
```

> 问题：在当前代码逻辑下，如果一个普通用户直接访问管理后台的主页，或者访问后续实现的视图函数路由，是直接可以进入的，如何解决？

