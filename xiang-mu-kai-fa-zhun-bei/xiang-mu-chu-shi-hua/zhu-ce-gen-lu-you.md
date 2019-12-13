# 注册根路由 \|

## 创建模板目录 <a id="&#x521B;&#x5EFA;&#x6A21;&#x677F;&#x76EE;&#x5F55;"></a>

* 在 **static** 同级目录创建 **templates** 文件夹，并在此目录下创建 **news** 文件夹用于存放新闻前台模板文件
* 设置 **templates** 目录成模板目录属性
* 此步可以省略，只是在 Pycharm 中标识该目录为模板目录
* 操作方式：右键点击 **templates** 目录，选择 `Mark Directory as` -&gt; `Template Folder`
* 如果没有设置模板语言，会弹出是否设置模板语言，点击 **Yes**，跳转到模板语言设置界面，设置模板语言为 Jinja2 

## 添加模板并创建根路由视图函数 <a id="&#x6DFB;&#x52A0;&#x6A21;&#x677F;&#x5E76;&#x521B;&#x5EFA;&#x6839;&#x8DEF;&#x7531;&#x89C6;&#x56FE;&#x51FD;&#x6570;"></a>

* 使用 Pycharm 将 `static/news/index.html` 文件移动到 `templates/news/` 目录下，并在 `index.py` 中添加根路由访问视图

```text
from . import index_blu
from flask import render_template


@index_blu.route('/')
def index():
    return render_template('news/index.html')
```

> 运行，访问根路由测试

## 网站图标展示 <a id="&#x7F51;&#x7AD9;&#x56FE;&#x6807;&#x5C55;&#x793A;"></a>

* 自定义视图函数，访问网站图标，send\_static\_file 是系统访问静态文件所调用的方法

```text
@index_blu.route('/favicon.ico')
def favicon():
    return current_app.send_static_file('news/favicon.ico')
```

