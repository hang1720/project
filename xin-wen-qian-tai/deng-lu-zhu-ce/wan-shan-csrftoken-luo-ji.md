# 完善CSRFToken逻辑 \|

## 理清思路 <a id="&#x7406;&#x6E05;&#x601D;&#x8DEF;"></a>

目前登录注册发起的 POST 请求均未进行 csrf\_token 校验，根据 csrf\_token 校验原理，具体操作步骤有以下几步：

1. 后端生成 csrf\_token 的值，在前端请求登录或者注册界面的时候将值传给前端，传给前端的方式可能有以下两种：
   * 在模板中的 From 表单中添加隐藏字段
   * 将 csrf\_token 使用 cookie 的方式传给前端
2. 在前端发起请求时，在表单或者在请求头中带上指定的 csrf\_token
3. 后端在接受到请求之后，取到前端发送过来的 csrf\_token，与第1步生成的 csrf\_token 的值进行校验
4. 如果校验对 csrf\_token 一致，则代表是正常的请求，否则可能是伪造请求，不予通过

而在 Flask 中，CSRFProtect 这个类专门只对指定 app 进行 csrf\_token **校验**操作，所以开发者需要做以下几件事情：

* 生成 csrf\_token 的值
* 将 csrf\_token 的值传给前端浏览器
* 在前端请求时带上 csrf\_token 值

## 完成代码逻辑 <a id="&#x5B8C;&#x6210;&#x4EE3;&#x7801;&#x903B;&#x8F91;"></a>

* 生成 csrf\_token 的值

```text

from flask_wtf.csrf import generate_csrf

csrf_token = generate_csrf()
```

* 将 csrf\_token 的值传给前端浏览器
  * 实现思路：可以在请求勾子函数中完成此逻辑

```text
@app.after_request
def after_request(response):
    
    csrf_token = generate_csrf()
    
    response.set_cookie("csrf_token", csrf_token)
    return response
```

* 在前端请求时带上 csrf\_token 值
  * 根据登录和注册的业务逻辑，当前采用的是 ajax 请求
  * 所以在提交登录或者注册请求时，需要在请求头中添加 `X-CSRFToken` 的键值对

```text
$.ajax({
    url:"/passport/register",
    type: "post",
    headers: {
        "X-CSRFToken": getCookie("csrf_token")
    },
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
```

> 打开 CSRFProtect 的代码，并运行测试 注：`X-CSRFToken` 这个 key 是 `CSRFProtect` 这个类里面指定的，具体请点击进入到此类查看源代码

