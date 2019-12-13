# 详情页用户数据展示 \|

* 如果当前用户已登录，查询当前登录用户数据，并在渲染模板时返回

```text
@news_blu.route('/<int:news_id>')
def news_detail(news_id):
    
    user_id = session.get("user_id")
    
    user = None
    if user_id:
        try:
            user = User.query.get(user_id)
        except Exception as e:
            current_app.logger.error(e)
    data = {
        "user_info": user.to_dict() if user else None,
    }
    return render_template('news/detail.html', data=data)
```

通过观察发现，首页视图函数也存在相同代码，都是如果用户登录就获取用户登录数据，没有登录就返回None，所以可以进行代码抽取。实现思路：

* 使用装饰器去加载用户数据并记录到 g 变量
* 在当前请求中可以直接使用 g 变量取到用户数据

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 common.py 中装饰器函数

```text
def user_login_data(f):
    @functools.wraps(f)
    def wrapper(*args, **kwargs):
        
        user_id = session.get("user_id")
        
        user = None
        if user_id:
            from info.models import User
            user = User.query.get(user_id)

        g.user = user
        return f(*args, **kwargs)

    return wrapper
```

* 修改 `index/views.py` 中根路由视图函数逻辑

```text
@index_blu.route('/')
@user_login_data
def index():

    <注：查询用户数据的代码已删除>
    ...
    data = {
        "user_info": g.user.to_dict() if g.user else None,
        "click_news_list": click_news_list,
        "categories": categories_dicts
    }

    return render_template('news/index.html', data=data)
```

* 修改 `news/views.py` 中新闻详情视图函数逻辑

```text
@news_blu.route('/<int:news_id>')
@user_login_data
def news_detail(news_id):
    data = {
        "user_info": g.user.to_dict() if g.user else None,
    }
    return render_template('news/detail.html', data=data)
```

> 运行测试，g 变量是一个应用上下文变量，类似于一个全局变量，但是 g 变量里面的保存的值是相对于每次请求的，不同的请求，g 变量里面保存的值是不同的，所以同一次请求，可以使用 g 变量来保存值用于进行函数的传递。

