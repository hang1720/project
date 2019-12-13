# 新闻版式编辑详情 \|

## 编辑详情界面数据 <a id="&#x7F16;&#x8F91;&#x8BE6;&#x60C5;&#x754C;&#x9762;&#x6570;&#x636E;"></a>

* 在 `admin/views.py` 中添加新闻编辑详情视图函数，接收新闻id为参数

```text
@admin_blu.route('/news_edit_detail')
def news_edit_detail():
    """新闻编辑详情"""

    
    news_id = request.args.get("news_id")

    if not news_id:
        return render_template('admin/news_edit_detail.html', data={"errmsg": "未查询到此新闻"})

    
    news = None
    try:
        news = News.query.get(news_id)
    except Exception as e:
        current_app.logger.error(e)

    if not news:
        return render_template('admin/news_edit_detail.html', data={"errmsg": "未查询到此新闻"})

    
    categories = Category.query.all()
    categories_li = []
    for category in categories:
        c_dict = category.to_dict()
        c_dict["is_selected"] = False
        if category.id == news.category_id:
            c_dict["is_selected"] = True
        categories_li.append(c_dict)
    
    categories_li.pop(0)

    data = {"news": news.to_dict(), "categories": categories_li}
    return render_template('admin/news_edit_detail.html', data=data)
```

* 修改待审核列表中 `news_review.html` 审核的链接地址:

```text
<td>
    <a href="{{ url_for('admin.news_edit_detail') }}?news_id={{ news.id }}" class="edit">编辑</a>
</td>
```

* `news_edit_detail.html` 数据渲染

```text
<div class="form_group">
    <label>新闻标题：</label>
    <input name="title" type="text" class="input_txt2" value="{{ data.news.title }}">
</div>
<div class="form_group">
    <label>新闻分类：</label>
    <select class="sel_opt" name="category_id">
        {% for category in data.categories %}
            <option value="{{ category.id }}" {% if category.is_selected %}selected{% endif %}>{{ category.name }}</option>
        {% endfor %}
    </select>
</div>
<div class="form_group">
    <label>新闻摘要：</label>
    <textarea class="input_multxt" name="digest">{{ data.news.digest }}</textarea>
</div>
<div class="form_group">
    <label>索引图片：</label>
    <img src="{{ data.news.index_image_url }}" alt="索引图片" class="index_pic">
</div>
<div class="form_group">
    <label>上传图片：</label>
    <input type="file" name="index_image" class="input_file">
</div>
<div class="form_group">
    <label>新闻内容：</label>
    <div class="rich_wrap fl">
        <input class="input_area" id="rich_content" name="content" value="{{ data.news.content }}"></input>
    </div>
</div>
{# 添加隐藏字段新闻id #}
<input type="hidden" name="news_id" value="{{ data.news.id }}">
```

## 新闻编辑提交 <a id="&#x65B0;&#x95FB;&#x7F16;&#x8F91;&#x63D0;&#x4EA4;"></a>

* 修改视图函数，使其能够接受POST请求执行保存编辑操作，并实现其代码

```text
@admin_blu.route('/news_edit_detail', methods=["GET", "POST"])
def news_edit_detail():
    """新闻编辑详情"""
    if request.method == "GET":
        ...
        return render_template('admin/news_edit_detail.html', data=data)

    news_id = request.form.get("news_id")
    title = request.form.get("title")
    digest = request.form.get("digest")
    content = request.form.get("content")
    index_image = request.files.get("index_image")
    category_id = request.form.get("category_id")
    
    if not all([title, digest, content, category_id]):
        return jsonify(errno=RET.PARAMERR, errmsg="参数有误")

    news = None
    try:
        news = News.query.get(news_id)
    except Exception as e:
        current_app.logger.error(e)
    if not news:
        return jsonify(errno=RET.NODATA, errmsg="未查询到新闻数据")

    
    if index_image:
        try:
            index_image = index_image.read()
        except Exception as e:
            current_app.logger.error(e)
            return jsonify(errno=RET.PARAMERR, errmsg="参数有误")

        
        try:
            key = storage(index_image)
        except Exception as e:
            current_app.logger.error(e)
            return jsonify(errno=RET.THIRDERR, errmsg="上传图片错误")
        news.index_image_url = constants.QINIU_DOMIN_PREFIX + key
    
    news.title = title
    news.digest = digest
    news.content = content
    news.category_id = category_id

    
    try:
        db.session.commit()
    except Exception as e:
        current_app.logger.error(e)
        db.session.rollback()
        return jsonify(errno=RET.DBERR, errmsg="保存数据失败")
    
    return jsonify(errno=RET.OK, errmsg="编辑成功")
```

* 在 `static/admin/js/news_edit_detail.js` 中添加以下代码

```text
$(function(){
    $(".news_edit").submit(function (e) {
        e.preventDefault()

        $(this).ajaxSubmit({
            beforeSubmit: function (request) {
                
                for(var i=0; i<request.length; i++) {
                    var item = request[i]
                    if (item["name"] == "content") {
                        item["value"] = tinyMCE.activeEditor.getContent()
                    }
                }
            },
            url: "/admin/news_edit_detail",
            type: "POST",
            headers: {
                "X-CSRFToken": getCookie('csrf_token')
            },
            success: function (resp) {
                if (resp.errno == "0") {
                    
                    location.href = document.referrer;
                } else {
                    alert(resp.errmsg);
                }
            }
        })
    })
})
```

> 注意：在提交的时候需要对参数进行处理，因为在编辑内容的时候，界面上的 input 标签内部的值是不会发生改变的，tinymce 的内容需要通过其指定方法获取，

