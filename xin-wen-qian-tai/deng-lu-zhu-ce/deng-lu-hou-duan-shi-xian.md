# 登录后端实现 \|

## 接口设计 <a id="&#x63A5;&#x53E3;&#x8BBE;&#x8BA1;"></a>

* URL：/passport/login
* 请求方式：POST
* 传入参数：JSON格式
* 参数

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| mobile | string | 是 | 手机号 |
| password | string | 是 | 密码 |

* 返回类型：JSON

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| errno | int | 是 | 错误码 |
| errmsg | string | 是 | 错误信息 |

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 需要校验数据是否合法，避免不必要的数据库查询操作
* 在 `info/passport/views.py` 文件中添加登录的路由

```text
@passport_blu.route('/login', methods=["POST"])
def login():
    """
    1. 获取参数和判断是否有值
    2. 从数据库查询出指定的用户
    3. 校验密码
    4. 保存用户登录状态
    5. 返回结果
    :return:
    """

    
    json_data = request.json

    mobile = json_data.get("mobile")
    password = json_data.get("password")

    if not all([mobile, password]):
        
        return jsonify(errno=RET.PARAMERR, errmsg="参数不全")

    
    try:
        user = User.query.filter_by(mobile=mobile).first()
    except Exception as e:
        current_app.logger.error(e)
        return jsonify(errno=RET.DBERR, errmsg="查询数据错误")

    if not user:
        return jsonify(errno=RET.USERERR, errmsg="用户不存在")

    
    if not user.check_passowrd(password):
        return jsonify(errno=RET.PWDERR, errmsg="密码错误")

    
    session["user_id"] = user.id
    session["nick_name"] = user.nick_name
    session["mobile"] = user.mobile
    
    user.last_login = datetime.now()
    try:
        db.session.commit()
    except Exception as e:
        current_app.logger.error(e)
    
    return jsonify(errno=RET.OK, errmsg="OK")
```

> 配置 SQLAlchemy SQLALCHEMY\_COMMIT\_ON\_TEARDOWN 属性实现请求结束之后进行数据自动提交

