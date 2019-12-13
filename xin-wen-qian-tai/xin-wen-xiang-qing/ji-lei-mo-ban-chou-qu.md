# 基类模板抽取 \|

## 分析 <a id="&#x5206;&#x6790;"></a>

* 在打开新闻详情页之后，观察很容易得出详情面和首页有相同的东西，
* 顶部 icon 和登录注册，右侧点击排行，底部网站信息等
* 可以将共同内容抽取到基类模板中，再使用其他模板继承基类模板

## 抽取 <a id="&#x62BD;&#x53D6;"></a>

* 在模板文件夹下创建 `base.html`
  * 将首页内容全部拷贝到该文件中，将特有的东西留作 block 给子模板实现
  * 抽取出来的block为：
    * 标题
    * script内容
    * 顶部中心
    * 页面内容
    * 点击排行div中的作者信息
  * 具体见以下代码

```text
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{% block titleBlock %}新经资讯网{% endblock %}</title>
    <link rel="stylesheet" type="text/css" href="../../static/news/css/reset.css">
    <link rel="stylesheet" type="text/css" href="../../static/news/css/main.css">
    <script type="text/javascript" src="../../static/news/js/jquery-1.12.4.min.js"></script>
    <script type="text/javascript" src="../../static/news/js/main.js"></script>
    {% block scriptBlock %}{% endblock %}
</head>
<body>
    <div class="header_con">
        <div class="header">
            <a href="#" class="logo fl"><img src="../../static/news/images/logo.png" alt="logo"></a>

            {% block headerBlock %}

            {% endblock %}

            {# 判断用户是否登录 #}
            {% if data.user_info %}
                {# 如果登录，则显示用户信息 #}
                <div class="user_login fr">
                    <img src="{% if data.user_info.avatar_url %} {{ data.user_info.avatar_url }} {% else %}../../static/news/images/person01.png{% endif %}" class="lgin_pic">
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

    <div class="conter_con">
        {% block contentBlock %}

        {% endblock %}
        {% block rankBlock %}
            <div class="rank_con fr">
                {% block contentBlock %}

                {% endblock %}
                <div class="rank_title">
                    <h3>点击排行</h3>
                </div>
                <ul class="rank_list">
                    {% for news in data.click_news_list %}
                        <li><span class="{{ loop.index0 | index_class }}">{{ loop.index }}</span><a href="/news/{{ news.id }}" target="_blank">{{ news.title }}</a></li>
                    {% endfor %}
                </ul>
            </div>
        {% endblock %}
    </div>

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

    
    <form class="login_form_con"...>

    
    <form class="register_form_con"...>
</body>
</html>
```

* index.html 继承实现为：

```text
{% extends 'news/base.html' %}

{% block titleBlock %}
首页-新经资讯
{% endblock %}

{% block scriptBlock %}
<script type="text/javascript" src="../../static/news/js/index.js"></script>
{% endblock %}

{% block headerBlock %}
<ul class="menu fl">
    {% for category in data.categories %}
        <li class="{% if category.active %}active{% endif %}" data-cid="{{ category.id }}"><a href="javascript:;">{{ category.name }}</a></li>
    {% endfor %}
</ul>
{% endblock %}

{% block contentBlock %}
<ul class="list_con fl">
</ul>
{% endblock %}
```

> 运行测试

* detail.html 继承实现为：

```text
{% extends 'news/base.html' %}
{% block titleBlock %}
文章详情页
{% endblock %}

{% block contentBlock %}
<div class="detail_con fl"...>
{% endblock %}

{% block authorBlock %}
<div class="author_card"...>
{% endblock %}
```

* 在详情页的视图函数添加数据传递\(为了测试运行\)

```text
@news_blu.route('/<int:news_id>')
def news_detail(news_id):
    data = {}
    return render_template('news/detail.html', data=data)
```

> 运行测试

