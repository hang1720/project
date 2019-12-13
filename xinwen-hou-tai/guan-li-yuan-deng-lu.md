# 管理员登录 \|

## 需求分析 <a id="&#x9700;&#x6C42;&#x5206;&#x6790;"></a>

* 管理员用户进行登录，并且根据不同的情况报出不同的错误信息
* 如果当前已登录用户是管理员，在访问登录页面时直接跳转到后台管理主页
* 登录界面可以直接使用 Form 表单提交\(也可以采用 ajax 的方式\)

## 代码准备 <a id="&#x4EE3;&#x7801;&#x51C6;&#x5907;"></a>

* 在 **templates** 目录下创建 `admin` 文件夹，将 `static/admin/`目录下 `login.html` 与 `index.html` 拖动到 `admin` 目录下

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `admin/views.py` 中，添加登录视图函数

```text
@admin_blu.route('/login')
def admin_login():
    return render_template('admin/login.html')
```

> 访问测试

* 修改上一步视图函数，添加 post 请求方式，并添加登录相关逻辑

```text
@admin_blu.route('/login', methods=["GET", "POST"])
def admin_login():
    if request.method == "GET":    
        return render_template('admin/login.html')

    
    username = request.form.get("username")
    password = request.form.get("password")
    if not all([username, password]):
        return render_template('admin/login.html', errmsg="参数不足")

    try:
        user = User.query.filter(User.mobile == username).first()
    except Exception as e:
        current_app.logger.error(e)
        return render_template('admin/login.html', errmsg="数据查询失败")

    if not user:
        return render_template('admin/login.html', errmsg="用户不存在")

    if not user.check_passowrd(password):
        return render_template('admin/login.html', errmsg="密码错误")

    if not user.is_admin:
        return render_template('admin/login.html', errmsg="用户权限错误")

    session["user_id"] = user.id
    session["nick_name"] = user.nick_name
    session["mobile"] = user.mobile
    session["is_admin"] = True

    
    return "登录成功，需要跳转到主页"
```

* 前端登录页面模板实现

```text
<form method="post" class="login_form">
    <h1 class="login_title">用户登录</h1>
    <input type="text" name="username" placeholder="用户名" class="input_txt">
    <input type="password" name="password" placeholder="密码" class="input_txt">
    {% if errmsg %}
        <div class="error_tip" style="display: block">{{ errmsg }}</div>
    {% endif %}
    <input type="submit" value="登 录" class="input_sub">
</form>
```

> 尝试运行，发现界面提示 `The CSRF token is missing.`

* 在 `admin/login.html` 的登录表单中添加 csrf\_token

```text
<form method="post" class="login_form">
    <h1 class="login_title">用户登录</h1>
    <input type="hidden" name="csrf_token" value="{{ csrf_token() }}" />
    ...
</form>
```

> 再将刷新测试，能够正常登录，登录成功跳转主页以及已登录状态下访问登录页面跳转主页后续再实现

