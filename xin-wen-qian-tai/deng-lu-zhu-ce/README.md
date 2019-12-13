# 登录注册 \|

## 登录注册 <a id="&#x767B;&#x5F55;&#x6CE8;&#x518C;"></a>

### 学习目标 <a id="&#x5B66;&#x4E60;&#x76EE;&#x6807;"></a>

* 能够说出当前项目注册的图片验证码验证逻辑
* 能够写出后端提供图片验证码的视图函数逻辑
* 能够说出发送短信验证码之前图片验证码校验的整个流程
* 能够参考课件步骤写出发送短信验码的后端代码逻辑
* 能够说出注册之前短信验证码校验的整个流程
* 能够参考课件步骤写出注册功能的后端代码逻辑
* 能够说出不使用表单的情况下完成 csrf 校验的流程
* 能够使用代码实现登录有后端逻辑
* 能够使用代码实现退出登录后端逻辑

### 功能分析 <a id="&#x529F;&#x80FD;&#x5206;&#x6790;"></a>

* 注册功能需求：
  * 使用手机号当前用户名进行注册
  * 注册的时候需要使用短信验证码进行验证
  * 在发送短信验证码之前需要验证图片验证码，以防止恶意发送短信验证码
* 登录功能需求
  * 输入手机号和密码进行登录
  * 登录失败有相应错误提示

## 登录注册模块蓝图 <a id="&#x767B;&#x5F55;&#x6CE8;&#x518C;&#x6A21;&#x5757;&#x84DD;&#x56FE;"></a>

* 在 **modules** 文件夹下创建 Package 取名为 **passport**，并在此目录下创建`views.py` 文件
* 在 **passport** 模块下的 `__init__.py` 文件中创建当前模块所使用的蓝图

```text
from flask import Blueprint

passport_blu = Blueprint("passport", __name__, url_prefix='/passport')

from . import views
```

* 在 app 创建的函数中去注册该蓝图

```text
def create_app(config_name):
    ...
    
    from info.modules.passport import passport_blu
    app.register_blueprint(passport_blu)

    return app
```

