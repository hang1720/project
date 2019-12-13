# 关注与取消关注 \|

## 接口设计 <a id="&#x63A5;&#x53E3;&#x8BBE;&#x8BA1;"></a>

* URL：/news/followed\_user
* 请求方式：POST
* 传入参数：JSON格式
* 参数

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| user\_id | int | 是 | 被关注的用户id |
| action | string | 是 | 指定两个值：'follow', 'unfollow' |

* 返回类型：JSON

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| errno | int | 是 | 错误码 |
| errmsg | string | 是 | 错误信息 |

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `modules/news/views.py` 中添加视图函数

```text
@news_blu.route('/followed_user', methods=["POST"])
@user_login_data
def followed_user():
    """关注/取消关注用户"""
    if not g.user:
        return jsonify(errno=RET.SESSIONERR, errmsg="用户未登录")

    user_id = request.json.get("user_id")
    action = request.json.get("action")

    if not all([user_id, action]):
        return jsonify(errno=RET.PARAMERR, errmsg="参数错误")

    if action not in ("follow", "unfollow"):
        return jsonify(errno=RET.PARAMERR, errmsg="参数错误")

    
    try:
        target_user = User.query.get(user_id)
    except Exception as e:
        current_app.logger.error(e)
        return jsonify(errno=RET.DBERR, errmsg="查询数据库失败")

    if not target_user:
        return jsonify(errno=RET.NODATA, errmsg="未查询到用户数据")

    
    if action == "follow":
        if target_user.followers.filter(User.id == g.user.id).count() > 0:
            return jsonify(errno=RET.DATAEXIST, errmsg="当前已关注")
        target_user.followers.append(g.user)
    else:
        if target_user.followers.filter(User.id == g.user.id).count() > 0:
            target_user.followers.remove(g.user)

    
    try:
        db.session.commit()
    except Exception as e:
        current_app.logger.error(e)
        return jsonify(errno=RET.DBERR, errmsg="数据保存错误")

    return jsonify(errno=RET.OK, errmsg="操作成功")
```

* 在 `detail.js` 中实现关注与取消关注的请求

```text

$(".focus").click(function () {
    var user_id = $(this).attr('data-userid')
    var params = {
        "action": "follow",
        "user_id": user_id
    }
    $.ajax({
        url: "/news/followed_user",
        type: "post",
        contentType: "application/json",
        headers: {
            "X-CSRFToken": getCookie("csrf_token")
        },
        data: JSON.stringify(params),
        success: function (resp) {
            if (resp.errno == "0") {
                
                var count = parseInt($(".follows b").html());
                count++;
                $(".follows b").html(count + "")
                $(".focus").hide()
                $(".focused").show()
            }else if (resp.errno == "4101"){
                
                $('.login_form_con').show();
            }else {
                
                alert(resp.errmsg)
            }
        }
    })
})


$(".focused").click(function () {
    var user_id = $(this).attr('data-userid')
    var params = {
        "action": "unfollow",
        "user_id": user_id
    }
    $.ajax({
        url: "/news/followed_user",
        type: "post",
        contentType: "application/json",
        headers: {
            "X-CSRFToken": getCookie("csrf_token")
        },
        data: JSON.stringify(params),
        success: function (resp) {
            if (resp.errno == "0") {
                
                var count = parseInt($(".follows b").html());
                count--;
                $(".follows b").html(count + "")
                $(".focus").show()
                $(".focused").hide()
            }else if (resp.errno == "4101"){
                
                $('.login_form_con').show();
            }else {
                
                alert(resp.errmsg)
            }
        }
    })
})
```

> 运行测试

