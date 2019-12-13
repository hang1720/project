# 前端逻辑实现 \|

* 在 `news/static/user_base_info.js` 文件中添加请求逻辑

```text
$(function () {

    $(".base_info").submit(function (e) {
        e.preventDefault()

        var signature = $("#signature").val()
        var nick_name = $("#nick_name").val()
        var gender = $(".gender").val()

        if (!nick_name) {
            alert('请输入昵称')
            return
        }
        if (!gender) {
            alert('请选择性别')
        }

        var params = {
            "signature": signature,
            "nick_name": nick_name,
            "gender": gender
        }

        $.ajax({
            url: "/user/base_info",
            type: "post",
            contentType: "application/json",
            headers: {
                "X-CSRFToken": getCookie("csrf_token")
            },
            data: JSON.stringify(params),
            success: function (resp) {
                if (resp.errno == "0") {
                    
                    $('.user_center_name', parent.document).html(params['nick_name'])
                    $('#nick_name', parent.document).html(params['nick_name'])
                    $('.input_sub').blur()
                }else {
                    alert(resp.errmsg)
                }
            }
        })
    })
})
```

> 修改完毕需要更新父窗口中的内容

