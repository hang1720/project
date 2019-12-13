# 发送短信前端实现 \|

* 在 `main.js` 文件的 `sendSMSCode` 方法中，使用 ajax 进行发送短信请求

```text

function sendSMSCode() {
    
    $(".get_code").removeAttr("onclick");
    var mobile = $("#register_mobile").val();
    if (!mobile) {
        $("#register-mobile-err").html("请填写正确的手机号！");
        $("#register-mobile-err").show();
        $(".get_code").attr("onclick", "sendSMSCode();");
        return;
    }
    var imageCode = $("#imagecode").val();
    if (!imageCode) {
        $("#image-code-err").html("请填写验证码！");
        $("#image-code-err").show();
        $(".get_code").attr("onclick", "sendSMSCode();");
        return;
    }

    

    var params = {
        "mobile": mobile,
        "image_code": imageCode,
        "image_code_id": imageCodeId
    }

    $.ajax({
        
        url: "/passport/smscode",
        
        method: "POST",
        
        data: JSON.stringify(params),
        
        contentType: "application/json",
        
        dataType: "json",
        success: function (resp) {
            if (resp.errno == "0") {
                
                var num = 60;
                
                var t = setInterval(function () {
                    if (num == 1) {
                        
                        clearInterval(t);
                        
                        $(".get_code").html("获取验证码");
                        
                        $(".get_code").attr("onclick", "sendSMSCode();");
                    } else {
                        num -= 1;
                        
                        $(".get_code").html(num + "秒");
                    }
                }, 1000)
            } else {
                
                $("#register-sms-code-err").html(resp.errmsg);
                $("#register-sms-code-err").show();
                
                $(".get_code").attr("onclick", "sendSMSCode();");
                
                if (resp.errno == "4004") {
                    generateImageCode()
                }
            }
        }
    })
}
```

* 进行代码测试，发现在请求发送短信的时候提示如下：
* 暂时关闭 `csrf_token` 的校验，去 `init/__init__.py` 文件中注释 csrf\_token 保护功能，先将当前业务调通。

```text
def create_app(config_name):
    """通过传入不同的配置名字，初始化其对应配置的应用实例"""

    ...
    
    
    ...
    return app
```

* 再次运行测试，页面正常倒计时，说明短信发送成功

> 修改现有前端页面 bug

```text

$('.form_group').on('click',function(){
    $(this).children('input').focus()
})

$('.form_group input').on('focusin',function(){
    $(this).siblings('.input_tip').animate({'top':-5,'font-size':12},'fast')
    $(this).parent().addClass('hotline');
})
```

