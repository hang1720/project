# 图片验证码 \|

## 图片验证码验证流程 <a id="&#x56FE;&#x7247;&#x9A8C;&#x8BC1;&#x7801;&#x9A8C;&#x8BC1;&#x6D41;&#x7A0B;"></a>

* 前端页生成验证码编号，并将编号并提交到后台去请求验证码图片
* 后台生成图片验证码，并把验证码文字内容当作值，验证码编号当作key存储在 redis 中
* 后台把验证码图片当作响应返回给前端
* 前端申请发送短信验证码的时候带上第1步验证码编号和用户输入的验证码内容
* 后台取出验证码编号对应的验证码内容与前端传过来的验证码内容进行对比
* 如果一样，则向指定手机发送验证码，如果不一样，则返回验证码错误

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 实现 \info\static\news\js\main.js 中的 generateImageCode 方法
  * 步骤：
    1. 通过uuid生成验证码编号
    2. 拼接验证码地址
    3. 设置验证码图片标签的src

```text

function generateImageCode() {
    
    
    imageCodeId = generateUUID();

    
    var imageCodeUrl = "/passport/image_code?code_id=" + imageCodeId;
    
    $(".get_pic_code").attr("src", imageCodeUrl)
}
```

* 在 passport 目录下的 `view.py` 文件中添加获取验证码的路由 `image_code`
  * 步骤：
    1. 获取参数
    2. 生成验证码
    3. 删除之前验证码并保存当前验证码
    4. 错误处理
    5. 响应返回

```text
from . import passport_blu

@passport_blu.route('/image_code')
def get_image_code():
    pass
```

* 安装图片验证码所需要的 Pillow 模块

```text
pip install Pillow
```

* 导入验证码生成工具包和自定义状态码文件
  * 在业务逻辑目录 `info` 下创建 `utils` Package
  * 将 `captcha` 文件夹和 `response_code.py` 文件拷贝到该目录
* 根据步骤实现视图函数

```text
from flask import current_app, jsonify
from flask import make_response
from flask import request

from info import constants
from info import redis_store
from info.utils.captcha.captcha import captcha
from info.utils.response_code import RET
from . import passport_blu


@passport_blu.route('/image_code')
def get_image_code():
    """
    获取图片验证码
    :return:
    """

    
    code_id = request.args.get('code_id')
    
    name, text, image = captcha.generate_captcha()
    try:
        
        redis_store.setex('ImageCode_' + code_id, constants.IMAGE_CODE_REDIS_EXPIRES, text)
    except Exception as e:
        current_app.logger.error(e)
        return make_response(jsonify(errno=RET.DATAERR, errmsg='保存图片验证码失败'))

    
    resp = make_response(image)
    
    resp.headers['Content-Type'] = 'image/jpg'
    return resp
```

> 运行测试

* 实现一打开登录和注册就有验证码显示，在 main.js 文件中指定 js 点击回调中添加生成验证码的函数

```text

$('.register_btn').click(function () {
    $('.register_form_con').show();
    generateImageCode()
})



$('.to_register').click(function () {
    $('.login_form_con').hide();
    $('.register_form_con').show();
    generateImageCode()
})
```

