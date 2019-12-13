# 个人中心 \|

个人中心是使用 iframe 进行实现，左边各个页签对应不同的 html 子页面，那么，子页面更新数据之后要同步更新主页面相关联数据，可以采用 js 进行，所以当前页面数据交互还是采用 ajax 方式

## 学习目标 <a id="&#x5B66;&#x4E60;&#x76EE;&#x6807;"></a>

* 能够完成个人中心页面的基类模板抽取
* 能够写出基本资料设置的后端代码逻辑
* 能够说出为什么要使用第三方文件存储平台的原因
* 能够写出头像上传的后端代码逻辑
* 能够写出用户密码修改的后端代码逻辑
* 能够写出用户新闻收藏列表的后端代码逻辑
* 能够写出用户发布新闻的后端代码逻辑
* 能够写出展示用户发布的新闻后端代码逻辑

## 准备工作 <a id="&#x51C6;&#x5907;&#x5DE5;&#x4F5C;"></a>

个人中心页面的前端页面为 `news/user.html`，创建新闻详情页的模块`profile`，并在该模块下创建视图函数存储的 py 文件 `views.py`

* 创建蓝图

```text
from flask import Blueprint

profile_blu = Blueprint("profile", __name__, url_prefix='/user')

from . import views
```

* 注册蓝图

```text
def create_app(config_name):
    ...
    
    from info.modules.profile import profile_blu
    app.register_blueprint(profile_blu)
    ...
    return app
```

* 将`static/news/user.html`文件拖到模板文件夹中，并添加视图函数进行返回

```text
@profile_blu.route('/info')
@user_login_data
def get_user_info():
    """
    获取用户信息
    1. 获取到当前登录的用户模型
    2. 返回模型中指定内容
    :return:
    """

    user = g.user
    if not user:
        
        return redirect('/')

    data = {
        "user_info": user.to_dict(),
    }
    
    return render_template("news/user.html", data=data)
```

* 更改基类模板中的个人中心地址

```text
{# 如果登录，则显示用户信息 #}
<div class="user_login fr">
    <img src="{% if data.user_info.avatar_url %} {{ data.user_info.avatar_url }}{% else %}../../static/news/images/person01.png{% endif %}" class="lgin_pic">
    <a href="/user/info">{{ data.user_info.nick_name }}</a>
    <a href="javascript:;" onclick="logout()">退出</a>
</div>
```

> 运行测试

