# 前端逻辑实现 \|

* 在 `templates/news/user_news_release.html` 中引入 `jquery.form`，以及给要提交的字段添加上 name 属性

```text
<script type="text/javascript" src="../../static/news/js/jquery.form.min.js"></script>
```

* 在 `news/static/user_news_release.js` 文件中添加请求逻辑

```text
function getCookie(name) {
    var r = document.cookie.match("\\b" + name + "=([^;]*)\\b");
    return r ? r[1] : undefined;
}

$(function () {
    $(".release_form").submit(function (e) {
        e.preventDefault()
        
        $(this).ajaxSubmit({
            url: "/user/news_release",
            type: "POST",
            headers: {
                "X-CSRFToken": getCookie('csrf_token')
            },
            success: function (resp) {
                if (resp.errno == "0") {
                    
                    window.parent.fnChangeMenu(6)
                    
                    window.parent.scrollTo(0, 0)
                }else {
                    alert(resp.errmsg)
                }
            }
        })
    })
})
```

> 发布完毕跳转到用户新闻列表页

* 修改首页新闻列表数据查询逻辑，在 `index/views.py` 文件中，修改 `get_news_list` 函数逻辑，添加`已审核通过`的条件

```text
@index_blu.route('/newslist')
def get_news_list():
    ...
    
    filters = [News.status == 0]
    
    if category_id != "0":
        filters.append(News.category_id == category_id)
    ...;
```

