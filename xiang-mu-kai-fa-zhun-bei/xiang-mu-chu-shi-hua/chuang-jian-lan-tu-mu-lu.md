# 创建蓝图目录 \|

项目的蓝图模块可以按以下方式来分：

* 按功能模块来分，比如：用户模块、订单模块
* 按接口版本来分，某个版本的接口放一个文件夹下面

因为 `新经资讯` 项目是前后端不分离的项目，界面数据大部分都使用模板的形式进行渲染，很少涉及到通过接口的形式返回数据，所以本项目使用按功能模块来划分蓝图。

* 在 `info` 目录下创建 `modules` Package，创建完成如下：

> modules 存放当前项目所有的模块

* 在 `modules` 文件夹下创建 `index` 文件夹， 并在此文件夹下创建 `views.py` 文件，将 `manage.py` 中定义的路由拷贝至该文件中

```text
@app.route('/index')
def index():
    return 'index'
```

> 暂时忽略报错，此处的index文件夹就是一个模块，此处这样设置是参照后续 Django 项目目录结构设置，views 存放当前模块所有的视图函数

* 到这一步 `manage.py` 中内容基本上都抽取完成，剩余内容如下：

```text

from flask_script import Manager
from flask_migrate import Migrate, MigrateCommand
from info import create_app, db

app = create_app('development')


manager = Manager(app)


Migrate(app, db)
manager.add_command('db', MigrateCommand)

if __name__ == '__main__':
    manager.run()
```

* 在 `index` 文件夹中的 `__init__.py`创建其自已的蓝图

```text
from flask import Blueprint

index_blu = Blueprint("index", __name__)

from . import views
```

* 在 `views.py` 中导入蓝图，并使用该蓝图注册路由

```text
from . import index_blu

@index_blu.route('/index')
def index():
    return 'index'
```

* 将上一步创建出来的蓝图注册到 app 中

```text
def create_app(config_name):
    ...

    
    from info.modules.index import index_blu
    app.register_blueprint(index_blu)

    return app
```

> 运行测试

