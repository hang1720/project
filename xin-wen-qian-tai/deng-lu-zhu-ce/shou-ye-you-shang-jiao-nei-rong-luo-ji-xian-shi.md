# 首页右上角内容逻辑显示 \|

## 需求 <a id="&#x9700;&#x6C42;"></a>

* 当前用户未登录时，登录/注册按钮
* 当用户已登录时，显示用户头像和昵称，并提供退出入口

## 功能分析 <a id="&#x529F;&#x80FD;&#x5206;&#x6790;"></a>

* 需要在用户请求首页的时候去数据库查询用户数据
* 如果查询到用户数据，则传入模板中，进行渲染，再进行返回
* 如果未查询到，返回用户数据为 None，模板代码中自行判断

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `index/views.py` 中，对原有根路由函数进行改造：

```text
@index_blu.route('/')
def index():
    
    user_id = session.get("user_id")
    
    user = None
    if user_id:
        try:
            user = User.query.get(user_id)
        except Exception as e:
            current_app.logger.error(e)

    return render_template('news/index.html', data={"user_info": user.to_dict() if user else None})
```

* 在 `index.html` 使用模板语言进行逻辑判断

```text
<div class="header_con">
    <div class="header">
        <a href="#" class="logo fl"><img src="../../static/news/images/logo.png" alt="logo"></a>
        <ul class="menu fl">
            ......
        </ul>

        {# 判断用户是否登录 #}
        {% if data.user_info %}
            {# 如果登录，则显示用户信息 #}
            <div class="user_login fr">
                <img src="{% if data.user_info.avatar_url %}{{ data.user_info.avatar_url }}{% else %}../../static/news/images/person01.png{% endif %}" class="lgin_pic">
                <a href="#">{{ data.user_info.nick_name }}</a>
                <a href="#">退出</a>
            </div>
        {% else %}
            {# 如果没有登录，则显示登录注册按钮 #}
            <div class="user_btns fr">
                <a href="javascript:;" class="login_btn">登录</a> / <a href="javascript:;" class="register_btn">注册</a>
            </div>
        {% endif %}
    </div>
</div>
```

> 运行测试，注意更改 `.user_login` 的 css 样式中的 `display` 为 `block`

