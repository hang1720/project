# 我的关注 \|

## 需求分析 <a id="&#x9700;&#x6C42;&#x5206;&#x6790;"></a>

* 在个人页面点击 `我的关注` 需要以分页的形式展示当前用户关注的其他用户
* 可以进行取消关注
* 点击进行到作者详情页面

## 实现准备 <a id="&#x5B9E;&#x73B0;&#x51C6;&#x5907;"></a>

* 将 `static/news/user_follow.html` 拖到 `templates/news/` 目录下

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `modules/profile/views.py` 中添加视图函数，返回当前用户关注的所有作者

```text
@profile_blu.route('/user_follow')
@user_login_data
def user_follow():
    
    p = request.args.get("p", 1)
    try:
        p = int(p)
    except Exception as e:
        current_app.logger.error(e)
        p = 1

    user = g.user

    follows = []
    current_page = 1
    total_page = 1
    try:
        paginate = user.followed.paginate(p, constants.USER_FOLLOWED_MAX_COUNT, False)
        
        follows = paginate.items
        
        current_page = paginate.page
        
        total_page = paginate.pages
    except Exception as e:
        current_app.logger.error(e)

    user_dict_li = []

    for follow_user in follows:
        user_dict_li.append(follow_user.to_dict())
    data = {"users": user_dict_li, "total_page": total_page, "current_page": current_page}
    return render_template('news/user_follow.html', data=data)
```

* 修改 `news/user.html` 中的 `我的关注` 链接地址

```text
<li><a href="/user/user_follow" target="main_frame">我的关注</a></li>
```

* 填充 `user_follow.html` 模板页面内容

```text
<ul class="card_list_con">

    {% for user in data.users %}
        <li class="author_card card_list">
            <a href="#" class="author_pic"><img src="{% if user.avatar_url %}
            {{ user.avatar_url }}
            {% else %}
            ../../static/news/images/user_pic.png
            {% endif %}" alt="author_pic"></a>
            <a href="#" class="author_name">{{ user.nick_name }}</a>
            <div class="author_resume">{{ user.signature }}</div>
            <div class="writings"><span>总篇数</span><b>{{ user.news_count }}</b></div>
            <div class="follows"><span>粉丝</span><b>{{ user.followers_count }}</b></div>
            <a href="javascript:;" class="focused fr" data-userid="{{ user.id }}"><span class="out">已关注</span><span class="over">取消关注</span></a>
        </li>
    {% endfor %}
</ul>
<div id="pagination" class="page"></div>
<script>
    $(function() {
        $("#pagination").pagination({
            currentPage: {{ data.current_page }},
            totalPage: {{ data.total_page }},
            callback: function(current) {
                window.location = "/user/user_follow?p=" + current
            }
        });
    });
</script>
```

> 运行测试

* 在 `static/news/js/user_follow.js` 中实现取消关注功能

```text

$(".focused").click(function () {
    var user_id = $(this).attr('data-userid')
    var params = {
        "action": "unfollow",
        "user_id": user_id
    }
    $.ajax({
        url: "/news/followed_user",
        type: "post",
        contentType: "application/json",
        headers: {
            "X-CSRFToken": getCookie("csrf_token")
        },
        data: JSON.stringify(params),
        success: function (resp) {
            if (resp.errno == "0") {
                
                window.location.reload()
            }else if (resp.errno == "4101"){
                
                $('.login_form_con').show();
            }else {
                
                alert(resp.errmsg)
            }
        }
    })
})
```

> 运行测试

