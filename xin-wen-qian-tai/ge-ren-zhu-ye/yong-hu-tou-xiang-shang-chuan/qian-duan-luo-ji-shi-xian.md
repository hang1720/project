# 前端逻辑实现 \|

* 在 `user_pic_info.html` 顶部导入 `jquery` 和 `jquery.form`

```text
<script type="text/javascript" src="../../static/news/js/jquery-1.12.4.min.js"></script>
<script type="text/javascript" src="../../static/news/js/jquery.form.min.js"></script>
```

* 在 `news/static/user_pic_info.js` 文件中添加请求逻辑

```text
$(function () {

    $(".pic_info").submit(function (e) {
        e.preventDefault()

        
        $(this).ajaxSubmit({
            url: "/user/pic_info",
            type: "POST",
            headers: {
                "X-CSRFToken": getCookie('csrf_token')
            },
            success: function (resp) {
                if (resp.errno == "0") {
                    $(".now_user_pic").attr("src", resp.data.avatar_url)
                    $(".user_center_pic>img", parent.document).attr("src", resp.data.avatar_url)
                    $(".user_login>img", parent.document).attr("src", resp.data.avatar_url)
                }else {
                    alert(resp.errmsg)
                }
            }
        })
    })
})
```

> 修改完毕需要更新父窗口中的内容

