# 后台访问权限控制 \|

## 需求 <a id="&#x9700;&#x6C42;"></a>

* 解决普通用户登录之后直接访问后台具体的视图函数的问题
* 如果是普通用户访问后台的视图函数，直接跳转到项目主页，不再执行后续的逻辑判断
* 后台后续要实现多个视图函数，如果每一个函数内部都去判断用户权限，那么代码重复率高，冗余代码较多
* 所以得有一个统一判断入口，后台模块中，除了登录页面，后台的其他页面都要判断是否具有管理员权限
* 采用的方式为：请求勾子中的 before\_request，来请求之前进行判断

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `modules/admin/__init__.py` 文件中，添加请求勾子函数

```text
@admin_blu.before_request
def before_request():
    
    if not request.url.endswith(url_for("admin.admin_login")):
        user_id = session.get("user_id")
        is_admin = session.get("is_admin", False)

        if not user_id or not is_admin:
            
            return redirect('/')
```

* 完善退出登录相关代码，在退出登录时候，也要清空是否是管理员的相关数据
  * 在 `modules/passport/views.py` 中

```text
@passport_blu.route("/logout", methods=['POST'])
def logout():
    """
    清除session中的对应登录之后保存的信息
    :return:
    """
    session.pop('user_id', None)
    session.pop('nick_name', None)
    session.pop('mobile', None)
    session.pop('is_admin', None)

    
    return jsonify(errno=RET.OK, errmsg="OK")
```

