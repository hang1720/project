# 注册后端实现 \|

## 注册后端实现 <a id="&#x6CE8;&#x518C;&#x540E;&#x7AEF;&#x5B9E;&#x73B0;"></a>

### 接口设计 <a id="&#x63A5;&#x53E3;&#x8BBE;&#x8BA1;"></a>

* URL：/passport/register
* 请求方式：POST
* 传入参数：JSON格式
* 参数

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| mobile | string | 是 | 手机号 |
| smscode | string | 是 | 短信验证码 |
| password | string | 是 | 密码 |

* 返回类型：JSON

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| errno | int | 是 | 错误码 |
| errmsg | string | 是 | 错误信息 |

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `passport/views.py` 中添加 `register` 视图函数，具体实现如下：

```text
@passport_blu.route('/register', methods=["POST"])
def register():
    """
    1. 获取参数和判断是否有值
    2. 从redis中获取指定手机号对应的短信验证码的
    3. 校验验证码
    4. 初始化 user 模型，并设置数据并添加到数据库
    5. 保存当前用户的状态
    6. 返回注册的结果
    :return:
    """

    
    json_data = request.json
    mobile = json_data.get("mobile")
    sms_code = json_data.get("smscode")
    password = json_data.get("password")

    if not all([mobile, sms_code, password]):
        
        return jsonify(errno=RET.PARAMERR, errmsg="参数不全")

    
    try:
        real_sms_code = redis_store.get("SMS_" + mobile)
    except Exception as e:
        current_app.logger.error(e)
        
        return jsonify(errno=RET.DBERR, errmsg="获取本地验证码失败")

    if not real_sms_code:
        
        return jsonify(errno=RET.NODATA, errmsg="短信验证码过期")

    
    if sms_code != real_sms_code:
        return jsonify(errno=RET.DATAERR, errmsg="短信验证码错误")
    
    try:
        redis_store.delete("SMS_" + mobile)
    except Exception as e:
        current_app.logger.error(e)

    
    user = User()
    user.nick_name = mobile
    user.mobile = mobile
    
    user.password = password

    try:
        db.session.add(user)
        db.session.commit()
    except Exception as e:
        db.session.rollback()
        current_app.logger.error(e)
        
        return jsonify(errno=RET.DATAERR, errmsg="数据保存错误")
    
    session["user_id"] = user.id
    session["nick_name"] = user.nick_name
    session["mobile"] = user.mobile

    
    return jsonify(errno=RET.OK, errmsg="OK")
```

