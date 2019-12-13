# 注册前端实现 \|

* 在 `main.js` 中完善注册表单提交的逻辑

```text


$(".register_form_con").submit(function (e) {
    e.preventDefault()

    
    var mobile = $("#register_mobile").val()
    var smscode = $("#smscode").val()
    var password = $("#register_password").val()

    if (!mobile) {
        $("#register-mobile-err").show();
        return;
    }
    if (!smscode) {
        $("#register-sms-code-err").show();
        return;
    }
    if (!password) {
        $("#register-password-err").html("请填写密码!");
        $("#register-password-err").show();
        return;
    }

    if (password.length < 6) {
        $("#register-password-err").html("密码长度不能少于6位");
        $("#register-password-err").show();
        return;
    }

    var params = {
        "mobile": mobile,
        "smscode": smscode,
        "password": password,
    }

    $.ajax({
        url:"/passport/register",
        type: "post",
        data: JSON.stringify(params),
        contentType: "application/json",
        success: function (resp) {
            if (resp.errno == "0"){
                
                location.reload()
            }else {
                $("#register-password-err").html(resp.errmsg)
                $("#register-password-err").show()
            }
        }
    })
})
```

> 运行测试，并思考：为什么不直接使用 form 表单的提交，而是要使用 ajax 去提交

