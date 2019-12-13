# 项目基本配置 \|

## Config类 <a id="config&#x7C7B;"></a>

* 先在当前类中定义配置的类，并从中加载配置

```text
app = Flask(__name__)

class Config(object):
    """工程配置信息"""
    DEBUG = True

app.config.from_object(Config)
```

> 运行测试

## SQLAlchemy <a id="sqlalchemy"></a>

* 导入数据库扩展，并在配置中填写相关配置

```text
from flask_sqlalchemy import SQLAlchemy

...

class Config(object):
    """工程配置信息"""
    DEBUG = True
    
    SQLALCHEMY_DATABASE_URI = "mysql://root:mysql@127.0.0.1:3306/information"
    SQLALCHEMY_TRACK_MODIFICATIONS = False

app.config.from_object(Config)
db = SQLAlchemy(app)
```

* 在终端创建数据库

```text
mysql> create database information charset utf8;
```

> 运行测试

## Redis <a id="redis"></a>

* 创建redis存储对象，并在配置中填写相关配置

```text
import redis
...

class Config(object):
    """工程配置信息"""
    ...
    
    REDIS_HOST = "127.0.0.1"
    REDIS_PORT = 6379

app.config.from_object(Config)
db = SQLAlchemy(app)
redis_store = redis.StrictRedis(host=Config.REDIS_HOST, port=Config.REDIS_PORT)
```

> 运行测试

## CSRF <a id="csrf"></a>

* 包含请求体的请求都需要开启CSRF

```text
from flask_wtf.csrf import CSRFProtect
...
app.config.from_object(Config)
...
CSRFProtect(app)
```

> CSRFProtect只做验证工作，cookie中的 csrf\_token 和表单中的 csrf\_token 需要我们自己实现

## Session <a id="session"></a>

* 利用 flask-session扩展，将 session 数据保存到 Redis 中

```text
from flask_session import Session
...

class Config(object):
    """工程配置信息"""
    SECRET_KEY = "EjpNVSNQTyGi1VvWECj9TvC/+kq3oujee2kTfQUs8yCM6xX9Yjq52v54g+HVoknA"
    ...
    
    SESSION_TYPE = "redis" 
    SESSION_USE_SIGNER = True 
    SESSION_REDIS = redis.StrictRedis(host=REDIS_HOST, port=REDIS_PORT) 
    PERMANENT_SESSION_LIFETIME = 86400 

app.config.from_object(Config)
...
Session(app)
```

> 运行测试
>
> 文档地址：[http://pythonhosted.org/Flask-Session/](http://pythonhosted.org/Flask-Session/)

## Flask-Script与数据库迁移扩展 <a id="flaskscript&#x4E0E;&#x6570;&#x636E;&#x5E93;&#x8FC1;&#x79FB;&#x6269;&#x5C55;"></a>

```text
from flask_script import Manager
from flask_migrate import Migrate, MigrateCommand
...
manager = Manager(app)
Migrate(app, db)
manager.add_command('db', MigrateCommand)
...

if __name__ == '__main__':
    manager.run()
```

> 运行测试

