# 后端接口实现 \|

## 接口设计 <a id="&#x63A5;&#x53E3;&#x8BBE;&#x8BA1;"></a>

* URL：/user/pic\_info
* 请求方式：POST
* 传入参数：JSON格式
* 参数

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| avatar | file | 是 | 头像 |

* 返回类型：JSON

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| errno | int | 是 | 错误码 |
| errmsg | string | 是 | 错误信息 |

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `profile/views.py` 文件中将 `pic_info` 添加请求方式 `POST` 为设置用户数据添加逻辑

```text
@profile_blu.route('/pic_info', methods=["GET", "POST"])
@user_login_data
def pic_info():
    user = g.user
    if request.method == "GET":
        return render_template('news/user_pic_info.html', data={"user_info": user.to_dict()})

    
    try:
        avatar_file = request.files.get("avatar").read()
    except Exception as e:
        current_app.logger.error(e)
        return jsonify(errno=RET.PARAMERR, errmsg="读取文件出错")

    
    try:
        url = storage(avatar_file)
    except Exception as e:
        current_app.logger.error(e)
        return jsonify(errno=RET.THIRDERR, errmsg="上传图片错误")

    

    
    user.avatar_url = url
    
    try:
        db.session.commit()
    except Exception as e:
        current_app.logger.error(e)
        db.session.rollback()
        return jsonify(errno=RET.DBERR, errmsg="保存用户数据错误")

    
    return jsonify(errno=RET.OK, errmsg="OK", data={"avatar_url": constants.QINIU_DOMIN_PREFIX + url})
```

