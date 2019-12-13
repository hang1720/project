# 新闻分类管理 \|

## 需求 <a id="&#x9700;&#x6C42;"></a>

* 可以修改当前新闻分类名
* 可以添加新闻分类

## 实现准备 <a id="&#x5B9E;&#x73B0;&#x51C6;&#x5907;"></a>

* 将 `static/admin/news_type.html` 文件拖到 `templates/admin/` 目录下

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `admin/views.py` 文件中添加视图函数以返回分类列表

```text
@admin_blu.route('/news_category')
def get_news_category():
    
    categories = Category.query.all()
    
    categories_dicts = []

    for category in categories:
        
        cate_dict = category.to_dict()
        
        categories_dicts.append(cate_dict)

    categories_dicts.pop(0)
    
    return render_template('admin/news_type.html', data={"categories": categories_dicts})
```

* 修改 `templates/admin/index.html` 中【新闻版式编辑】链接

```text
<li><a class="icon034" href="/admin/news_type" target="main_frame">新闻分类管理</a></li>
```

* 前端页面界面数据渲染

```text
<tr>
    <th width="10%">id</th>
    <th width="80%">类别名称</th>
    <th width="10%">管理操作</th>
</tr>

{% for category in data.categories %}
    <tr>
        <td>{{ category.id }}</td>
        <td>{{ category.name }}</td>
        <td><a href="javascript:;" class="edit">编辑</a></td>
    </tr>
{% endfor %}
```

> 运行测试

* 增加【添加/修改】分类的视图函数

```text
@admin_blu.route('/add_category', methods=["POST"])
def add_category():
    """修改或者添加分类"""

    category_id = request.json.get("id")
    category_name = request.json.get("name")
    if not category_name:
        return jsonify(errno=RET.PARAMERR, errmsg="参数错误")
    
    if category_id:
        try:
            category = Category.query.get(category_id)
        except Exception as e:
            current_app.logger.error(e)
            return jsonify(errno=RET.DBERR, errmsg="查询数据失败")

        if not category:
            return jsonify(errno=RET.NODATA, errmsg="未查询到分类信息")

        category.name = category_name
    else:
        
        category = Category()
        category.name = category_name
        db.session.add(category)

    try:
        db.session.commit()
    except Exception as e:
        current_app.logger.error(e)
        db.session.rollback()
        return jsonify(errno=RET.DBERR, errmsg="保存数据失败")
    return jsonify(errno=RET.OK, errmsg="保存数据成功")
```

* 在 `/static/admin/js/news_type.js` 中发起请求

```text
$confirm.click(function(){
    var params = {}
    if(sHandler=='edit')
    {
        var sVal = $input.val();
        if(sVal=='')
        {
            $error.html('输入框不能为空').show();
            return;
        }
        params = {
            "id": sId,
            "name": sVal,
        };
    }
    else
    {
        var sVal = $input.val();
        if(sVal=='')
        {
            $error.html('输入框不能为空').show();
            return;
        }
        params = {
            "name": sVal,
        }
    }

    $.ajax({
        url:"/admin/add_category",
        method: "post",
        headers: {
            "X-CSRFToken": getCookie("csrf_token")
        },
        data: JSON.stringify(params),
        contentType: "application/json",
        success: function (resp) {
            if (resp.errno == "0") {
                
                location.reload();
            }else {
                $error.html(resp.errmsg).show();
            }
        }
    })
})
```

