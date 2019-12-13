# 404页面 \|

* 需求：在用户访问一些不存在网址的时候弹出404页面
* 实现逻辑：可以使用 app.errorhandle\(code\_or\_exception\) 装饰器

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 因为404页面没有底部的内容，所以在 `templates/news/base.html` 里面将底部的内容设置成为一个 block

```text
{% block bottomBlock %}
    <div class="footer">
        <div class="footer_links">
            <a href="">关于我们</a>
            <span>|</span>
            <a href="">联系我们</a>
            <span>|</span>
            <a href="">招聘人才</a>
            <span>|</span>
            <a href="">友情链接</a>
        </div>
        <p class="copyright">
            CopyRight © 2018 新经资讯信息技术有限公司 All Rights Reserved<br />
电话：010-****888    京ICP备*******8号
        </p>
    </div>
{% endblock %}
```

* 将 `static/news/404.html` 文件拖到 `templates/news/` 目录下，并继承于基类模板

```text
{% extends 'news/base.html' %}
{% block titleBlock %}
404
{% endblock %}

{% block contentBlock %}
    <div class="error_con">
        <img src="../../static/news/images/not_found.png" alt="404">
        <h3>OOPS!</h3>
        <h4>该内容不存在</h4>
        <p>请检查你输入的网址是否正确，请点击以下按钮返回主页或者发送错误报告</p>
        <a href="/">返回首页</a>
    </div>
{% endblock %}

{% block rankBlock %}
{% endblock %}

{% block bottomBlock %}
{% endblock %}
```

* 在 `info/__init__.py` 文件中的 `create_app` 函数中添加以下逻辑

```text

def create_app(config_name):
    ...

    @app.errorhandler(404)
    @user_login_data
    def page_not_found(_):
        user = g.user
        data = {"user_info": user.to_dict() if user else None}
        return render_template('news/404.html', data=data)
        return app
```

> 在浏览器里面输入网址进行测试

