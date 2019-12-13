# 用户列表 \|

## 需求 <a id="&#x9700;&#x6C42;"></a>

* 按用户最后一次登录倒序分页展示用户列表

## 实现准备 <a id="&#x5B9E;&#x73B0;&#x51C6;&#x5907;"></a>

* 将 `static/admin/user_list.html` 拖到 `templates/admin/` 目录下

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 后端逻辑

```text
@admin_blu.route('/user_list')
def user_list():
    """获取用户列表"""

    
    page = request.args.get("p", 1)
    try:
        page = int(page)
    except Exception as e:
        current_app.logger.error(e)
        page = 1

    
    users = []
    current_page = 1
    total_page = 1

    
    try:
        paginate = User.query.filter(User.is_admin == False).order_by(User.last_login.desc()).paginate(page, constants.ADMIN_USER_PAGE_MAX_COUNT, False)
        users = paginate.items
        current_page = paginate.page
        total_page = paginate.pages
    except Exception as e:
        current_app.logger.error(e)

    
    users_list = []
    for user in users:
        users_list.append(user.to_admin_dict())

    context = {"total_page": total_page, "current_page": current_page, "users": users_list}
    return render_template('admin/user_list.html',
                           data=context)
```

* 修改 `templates/admin/index.html` 中【用户列表】链接

```text
<li><a class="icon021" href="/admin/user_list" target="main_frame">用户列表</a></li>
```

* 前端页面界面渲染

```text
<div class="pannel">
    <table class="common_table">
        <tr>
            <th>用户名</th>
            <th>电话</th>
            <th>注册时间</th>
            <th>上次登录时间</th>
        </tr>
        {% for user in data.users %}
        <tr>
            <td>{{ user.nick_name }}</td>
            <td>{{ user.mobile }}</td>
            <td>{{ user.register }}</td>
            <td>{{ user.last_login }}</td>
        </tr>
        {% endfor %}
    </table>
</div>

<div class="box">
    <div id="pagination" class="page"></div>
</div>

<script>
    $(function() {
        $("#pagination").pagination({
            currentPage: {{ data.current_page }},
            totalPage: {{ data.total_page }},
            callback: function(current) {
                window.location.href = '/admin/user_list?p=' + current
            }
        });
    });
</script>
```

> 运行测试

