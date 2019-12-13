# 前端逻辑实现 \|

* 在 `templates/news/user_pass_info.html` 中给表单要提交的字段设置 `name` 值

```text
<div class="form-group">
    <label>当前密码：</label>
    <input type="password" name="old_password" class="input_txt">
</div>
<div class="form-group">
    <label>新密码：</label>
    <input type="password" name="new_password" class="input_txt">
</div>
<div class="form-group">
    <label>确认密码：</label>
    <input type="password" name="new_password2" class="input_txt">
</div>
<div class="error_tip">提示信息</div>
<div class="form-group">
    <input type="submit" value="保 存" class="input_sub">
</div>
```

* 在 `news/static/user_pass_info.js` 文件中添加请求逻辑

```text
$(function () {

    $(".pass_info").submit(function (e) {
        e.preventDefault();

        var params = {};
        $(this).serializeArray().map(function (x) {
            params[x.name] = x.value;
        });
        
        var new_password = params["new_password"];
        var new_password2 = params["new_password2"];

        if (new_password != new_password2) {
            alert('两次密码输入不一致')
            return
        }

        $.ajax({
            url: "/user/pass_info",
            type: "post",
            contentType: "application/json",
            headers: {
                "X-CSRFToken": getCookie("csrf_token")
            },
            data: JSON.stringify(params),
            success: function (resp) {
                if (resp.errno == "0") {
                    
                    alert("修改成功")
                    window.location.reload()
                }else {
                    alert(resp.errmsg)
                }
            }
        })
    })
})
```

> 修改完毕清空输入框内容（当前采用直接刷新当前界面的做法）

