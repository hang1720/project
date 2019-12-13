# 新闻后台 \|

## 新闻后台 <a id="&#x65B0;&#x95FB;&#x540E;&#x53F0;"></a>

## 学习目标 <a id="&#x5B66;&#x4E60;&#x76EE;&#x6807;"></a>

* 能够使用 Flask-script 扩展添加生成管理员的操作
* 能够使用请求勾子实现限制普通用户访问管理员相关界面的功能
* 能够参考课件使用代码实现用户统计的后端代码逻辑
* 能够使用代码实现用户列表的后端代码逻辑
* 能够实现新闻审核列表及详情的后端代码逻辑
* 能够实现新闻版式编辑列表及编辑详情的后端代码逻辑
* 能够使用代码实现新闻分类修改和添加的后端代码逻辑

### 功能需求 <a id="&#x529F;&#x80FD;&#x9700;&#x6C42;"></a>

* 通过命令行形式创建管理员用户\(flask-script扩展\)
* 管理员与普通用户共用一张表，管理员用户也具有普通用户的功能，是否是管理员使用指定字段区分
* 管理员用户可以登录到后台进行相应的数据查看以及新闻操作
* 打开管理员相关操作页面如果未登录或者角色不是管理员需要跳转到管理员登录页面

### 功能准备 <a id="&#x529F;&#x80FD;&#x51C6;&#x5907;"></a>

#### 创建管理员 <a id="&#x521B;&#x5EFA;&#x7BA1;&#x7406;&#x5458;"></a>

* 使用 flask-script 扩展添加命令行相关逻辑操作，在 `manage.py` 中添加以下代码

```text
@manager.option('-n', '-name', dest='name')
@manager.option('-p', '-password', dest='password')
def createsuperuser(name, password):
    """创建管理员用户"""
    if not all([name, password]):
        print('参数不足')
        return

    user = User()
    user.mobile = name
    user.nick_name = name
    user.password = password
    user.is_admin = True

    try:
        db.session.add(user)
        db.session.commit()
        print("创建成功")
    except Exception as e:
        print(e)
        db.session.rollback()
```

* 命令行执行：

```text
$ python manage.py createsuperuser -n admin -p 12345678
```

* 运行效果：

#### 创建后台管理模块 <a id="&#x521B;&#x5EFA;&#x540E;&#x53F0;&#x7BA1;&#x7406;&#x6A21;&#x5757;"></a>

**创建模块**

* 在 **modules** 目录下创建 `admin` Package，创建 `views.py` 文件做视图函数访问文件

**配置蓝图**

* 在 `admin/__init__.py` 文件中创建后台管理蓝图

```text
from flask import Blueprint

admin_blu = Blueprint("admin", __name__, url_prefix='/admin')

from . import views
```

* 注册蓝图

```text
def create_app(config_name):
    ...
    

    from info.modules.admin import admin_blu
    app.register_blueprint(admin_blu)
    ...
    return app
```

