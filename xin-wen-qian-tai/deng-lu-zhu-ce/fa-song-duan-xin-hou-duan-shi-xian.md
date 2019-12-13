# 发送短信后端实现 \|

* 发送短信验证码实现流程：
  1. 接收前端发送过来的请求参数
  2. 检查参数是否已经全部传过来
  3. 判断手机号格式是否正确
  4. 检查图片验证码是否正确，若不正确，则返回
  5. 删除图片验证码
  6. 生成随机的短信验证码
  7. 使用第三方SDK发送短信验证码

## 接口设计 <a id="&#x63A5;&#x53E3;&#x8BBE;&#x8BA1;"></a>

* URL：/passport/sms\_code
* 请求方式：POST
* 传入参数：JSON格式
* 参数

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| mobile | string | 是 | 手机号 |
| image\_code | string | 是 | 用户输入的图片验证码内容 |
| image\_code\_id | string | 是 | 真实图片验证码编号 |

* 返回类型：JSON

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| errno | int | 是 | 错误码 |
| errmsg | string | 是 | 错误信息 |

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `passport/views.py` 文件中，注册 `smscode` 路由，并实现代码

```text
@passport_blu.route('/smscode')
def send_sms():
    """
    1. 接收参数并判断是否有值
    2. 校验手机号是正确
    3. 通过传入的图片编码去redis中查询真实的图片验证码内容
    4. 进行验证码内容的比对
    5. 生成发送短信的内容并发送短信
    6. redis中保存短信验证码内容
    7. 返回发送成功的响应
    :return:
    """
    
    
    param_dict = request.json
    mobile = param_dict.get("mobile")
    image_code = param_dict.get("image_code")
    image_code_id = param_dict.get("image_code_id")

    if not all([mobile, image_code_id, image_code]):
        
        return jsonify(errno=RET.PARAMERR, errmsg="参数不全")

    
    if not re.match("^1[3578][0-9]{9}$", mobile):
        
        return jsonify(errno=RET.DATAERR, errmsg="手机号不正确")

    
    try:
        real_image_code = redis_store.get("ImageCode_" + image_code_id)
        
        if real_image_code:
            real_image_code = real_image_code.decode()
            redis_store.delete("ImageCode_" + image_code_id)
    except Exception as e:
        current_app.logger.error(e)
        
        return jsonify(errno=RET.DBERR, errmsg="获取图片验证码失败")
    
    if not real_image_code:
        
        return jsonify(errno=RET.NODATA, errmsg="验证码已过期")

    
    if image_code.lower() != real_image_code.lower():
        
        return jsonify(errno=RET.DATAERR, errmsg="验证码输入错误")

    
    try:
        user = User.query.filter_by(mobile=mobile).first()
    except Exception as e:
        current_app.logger.error(e)
        return jsonify(errno=RET.DBERR, errmsg="数据库查询错误")
    if user:
        
        return jsonify(errno=RET.DATAEXIST, errmsg="该手机已被注册")

    
    result = random.randint(0, 999999)
    sms_code = "%06d" % result
    current_app.logger.debug("短信验证码的内容：%s" % sms_code)
    result = CCP().send_template_sms(mobile, [sms_code, constants.SMS_CODE_REDIS_EXPIRES / 60], "1")
    if result != 0:
        
        return jsonify(errno=RET.THIRDERR, errmsg="发送短信失败")

    
    try:
        redis_store.set("SMS_" + mobile, sms_code, constants.SMS_CODE_REDIS_EXPIRES)
    except Exception as e:
        current_app.logger.error(e)
        
        return jsonify(errno=RET.DBERR, errmsg="保存短信验证码失败")

    
    return jsonify(errno=RET.OK, errmsg="发送成功")
```

