# 代码抽取 \|

* 目标：将特定逻辑代码抽取到指定的类中，各司其职，放便后续项目维护

## 配置文件 <a id="&#x914D;&#x7F6E;&#x6587;&#x4EF6;"></a>

* 在与 `manage.py` 同级目录下创建 `config.py` 文件，用作于项目的配置文件

```text

import redis


class Config(object):
    """工程配置信息"""
    SECRET_KEY = "EjpNVSNQTyGi1VvWECj9TvC/+kq3oujee2kTfQUs8yCM6xX9Yjq52v54g+HVoknA"

    DEBUG = True

    
    SQLALCHEMY_DATABASE_URI = "mysql://root:mysql@127.0.0.1:3306/information"
    SQLALCHEMY_TRACK_MODIFICATIONS = True

    
    REDIS_HOST = "127.0.0.1"
    REDIS_PORT = 6379

    
    SESSION_TYPE = "redis"  
    SESSION_USE_SIGNER = True  
    SESSION_REDIS = redis.StrictRedis(host=REDIS_HOST, port=REDIS_PORT)  
    PERMANENT_SESSION_LIFETIME = 86400  
```

* 在 `manager.py` 中引入 `Config` 类，直接使用

```text
from config import Config

app = Flask(__name__)


app.config.from_object(Config)
```

> 运行测试

### 业务逻辑独立 <a id="&#x4E1A;&#x52A1;&#x903B;&#x8F91;&#x72EC;&#x7ACB;"></a>

在整个项目文件夹中，除了启动文件 `manage.py` 和配置文件 `config.py` 放在根目录，其他具体业务逻辑文件都放在一个单独的文件夹内，与 `manage.py` 同级

* 创建 `info` Package，与 `manage.py` 同级
* `manage.py` 只做最基本的启动工作，将 `app` 的创建操作移动到 `info` 的 `__init__.py` 文件中

```text
import redis
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_wtf.csrf import CSRFProtect
from flask_session import Session
from config import Config

app = Flask(__name__)


app.config.from_object(Config)

db = SQLAlchemy(app)

redis_store = redis.StrictRedis(host=Config.REDIS_HOST, port=Config.REDIS_PORT)

CSRFProtect(app)

Session(app)
```

* `manage.py` 的代码为

```text
from flask_script import Manager
from flask_migrate import Migrate, MigrateCommand
from info import app, db


manager = Manager(app)

Migrate(app, db)
manager.add_command('db', MigrateCommand)


@app.route('/index')
def index():
    return 'index'


if __name__ == '__main__':
    manager.run()
```

> 运行测试

### 项目多种配置 <a id="&#x9879;&#x76EE;&#x591A;&#x79CD;&#x914D;&#x7F6E;"></a>

一个web程序在开发阶段可能与生产阶段所需要的配置信息可能不一样，所以为了实现此功能，可以给不同情况创建不同的配置类，比如开发阶段使用的配置类名为 `DevelopementConfig`，生产阶段使用的配置类名为 `ProdutionConfig`

* 修改 `config.py` 文件的配置文件如下

```text
import redis


class Config(object):
    """工程配置信息"""

    SECRET_KEY = "EjpNVSNQTyGi1VvWECj9TvC/+kq3oujee2kTfQUs8yCM6xX9Yjq52v54g+HVoknA"

    
    SQLALCHEMY_DATABASE_URI = "mysql://root:mysql@127.0.0.1:3306/information"
    SQLALCHEMY_TRACK_MODIFICATIONS = False

    
    REDIS_HOST = "127.0.0.1"
    REDIS_PORT = 6379

    
    SESSION_TYPE = "redis"  
    SESSION_USE_SIGNER = True  
    SESSION_REDIS = redis.StrictRedis(host=REDIS_HOST, port=REDIS_PORT)  
    PERMANENT_SESSION_LIFETIME = 86400  


class DevelopementConfig(Config):
    """开发模式下的配置"""
    DEBUG = True


class ProductionConfig(Config):
    """生产模式下的配置"""
    pass
```

> 接下来思考如何能才更快速的针对不同的布署环境去使用不同的配置

### 工厂类方法创建应用实例 <a id="&#x5DE5;&#x5382;&#x7C7B;&#x65B9;&#x6CD5;&#x521B;&#x5EFA;&#x5E94;&#x7528;&#x5B9E;&#x4F8B;"></a>

要在不同环境下去使用不同的配置，那么可以在 `manage.py` 文件中给 `info` 包传入不同的配置信息，让 ihome 去根据传入指定配置去创建 `app`，所以可以在 `info` 的 `__init__.py` 文件中添加一个工厂方法，根据传入的配置不同创建其对应的应用实例

* 在 `config.py` 文件中添加以下代码

```text
# 定义配置字典
config = {
    "development": DevelopementConfig,
    "production": ProductionConfig
}
```

* 修改 `info` 文件夹下 `__init__.py`，添加 `create_app` 的工厂方法

```text
def create_app(config_name):
    """通过传入不同的配置名字，初始化其对应配置的应用实例"""
    pass
```

* 修改 `manage.py` 文件中的代码

```text
from info import create_app, db


app = create_app('development')
```

* 将 `__init__.py` 文件中创建 app 实例的方法移动到 `create_app` 方法中

```text
from config import config



db = SQLAlchemy()
redis_store = None


def create_app(config_name):
    """通过传入不同的配置名字，初始化其对应配置的应用实例"""

    app = Flask(__name__)

    
    app.config.from_object(config[config_name])
    
    db.init_app(app)
    
    global redis_store
    redis_store = redis.StrictRedis(host=config[config_name].REDIS_HOST, port=config[config_name].REDIS_PORT)
    
    CSRFProtect(app)
    
    Session(app)

    return app
```

