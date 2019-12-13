# 登录前端实现 \|

* 在 `main.js` 中完登录表单提交的逻辑

```text

$(".login_form_con").submit(function (e) {
    e.preventDefault()
    var mobile = $(".login_form #mobile").val()
    var password = $(".login_form #password").val()

    if (!mobile) {
        $("#login-mobile-err").show();
        return;
    }

    if (!password) {
        $("#login-password-err").show();
        return;
    }

    var params = {
        "mobile": mobile,
        "password": password,
    }

    $.ajax({
        url:"/passport/login",
        method: "post",
        data: JSON.stringify(params),
        contentType: "application/json",
        success: function (resp) {
            if (resp.errno == "0") {
                
                location.reload();
            }else {
                $("#login-password-err").html(resp.errmsg)
                $("#login-password-err").show()
            }
        }
    })
})
```

> 运行测试

