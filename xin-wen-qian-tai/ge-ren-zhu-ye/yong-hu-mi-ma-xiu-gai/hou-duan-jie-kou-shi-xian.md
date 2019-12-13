# 后端接口实现 \|

## 接口设计 <a id="&#x63A5;&#x53E3;&#x8BBE;&#x8BA1;"></a>

* URL：/user/pass\_info
* 请求方式：POST
* 传入参数：JSON格式
* 参数

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| old\_password | string | 是 | 旧密码 |
| new\_password | string | 是 | 新密码 |

* 返回类型：JSON

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| errno | int | 是 | 错误码 |
| errmsg | string | 是 | 错误信息 |

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `profile/views.py` 文件中将 `pass_info` 添加请求方式 `POST` 为修改密码添加逻辑

```text
@profile_blu.route('/pass_info', methods=["GET", "POST"])
@user_login_data
def pass_info():
    if request.method == "GET":
        return render_template('news/user_pass_info.html')

    
    data_dict = request.json
    old_password = data_dict.get("old_password")
    new_password = data_dict.get("new_password")

    if not all([old_password, new_password]):
        return jsonify(errno=RET.PARAMERR, errmsg="参数有误")

    
    user = g.user

    if not user.check_passowrd(old_password):
        return jsonify(errno=RET.PWDERR, errmsg="原密码错误")

    
    user.password = new_password
    try:
        db.session.commit()
    except Exception as e:
        current_app.logger.error(e)
        db.session.rollback()
        return jsonify(errno=RET.DBERR, errmsg="保存数据失败")

    return jsonify(errno=RET.OK, errmsg="保存成功")
```

