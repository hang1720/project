# 收藏前端逻辑实现 \|

* 前端使用 ajax 进行请求后端提供的接口
* 根据接口返回的信息进行界面的展示判断
* 在 `detail.js` 中实现收藏和取消收藏请求

```text
$(function(){
    
    $(".collection").click(function () {
        var news_id = $(".collection").attr('data-newid');
        var action = "collect"
        var params = {
            "news_id": news_id,
            "action": action
        }
        $.ajax({
            url: "/news/news_collect",
            type: "post",
            contentType: "application/json",
            headers: {
                "X-CSRFToken": getCookie("csrf_token")
            },
            data: JSON.stringify(params),
            success: function (resp) {
                if (resp.errno == "0") {
                    
                    
                    $(".collection").hide();
                    
                    $(".collected").show();
                }else if (resp.errno == "4101"){
                    $('.login_form_con').show();
                }else{
                    alert(resp.errmsg);
                }
            }
        })
    })

    
    $(".collected").click(function () {
        var news_id = $(".collected").attr('data-newid');
        var action = "cancel_collect"
        var params = {
            "news_id": news_id,
            "action": action
        }
        $.ajax({
            url: "/news/news_collect",
            type: "post",
            contentType: "application/json",
            headers: {
                "X-CSRFToken": getCookie("csrf_token")
            },
            data: JSON.stringify(params),
            success: function (resp) {
                if (resp.errno == "0") {
                    
                    
                    $(".collection").show();
                    
                    $(".collected").hide();
                }else if (resp.errno == "4101"){
                    $('.login_form_con').show();
                }else{
                    alert(resp.errmsg);
                }
            }
        })
    })
})
```

> 运行测试

