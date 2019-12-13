# 登出功能实现 \|

## 需求分析 <a id="&#x9700;&#x6C42;&#x5206;&#x6790;"></a>

* 登录出功能本质就是删除之前登录保存到服务的 session 数据

### 后端代码实现 <a id="&#x540E;&#x7AEF;&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `possport/views.py` 文件中添加登出视图函数

```text
@passport_blu.route("/logout", methods=['POST'])
def logout():
    """
    清除session中的对应登录之后保存的信息
    :return:
    """
    session.pop('user_id', None)
    session.pop('nick_name', None)
    session.pop('mobile', None)

    
    return jsonify(errno=RET.OK, errmsg="OK")
```

### 前端代码实现 <a id="&#x524D;&#x7AEF;&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `news/main.js` 中添加登出函数，并使用 ajax 请求

```text
function logout() {
    $.ajax({
        url: "/passport/logout",
        type: "post",
        contentType: "application/json",
        headers: {
            "X-CSRFToken": getCookie("csrf_token")
        },
        success: function (resp) {
            
            location.reload()
        }
    })
}
```

