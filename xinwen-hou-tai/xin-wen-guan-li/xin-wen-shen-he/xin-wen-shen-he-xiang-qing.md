# 新闻审核详情 \|

## 审核详情界面数据 <a id="&#x5BA1;&#x6838;&#x8BE6;&#x60C5;&#x754C;&#x9762;&#x6570;&#x636E;"></a>

* 在 `admin/views.py` 中添加新闻审核详情视图函数，接收新闻id为参数

```text
@admin_blu.route('/news_review_detail')
def news_review_detail():
    """新闻审核"""

    
    news_id = request.args.get("news_id")
    if not news_id:
        return render_template('admin/news_review_detail.html', data={"errmsg": "未查询到此新闻"})

    
    news = None
    try:
        news = News.query.get(news_id)
    except Exception as e:
        current_app.logger.error(e)

    if not news:
        return render_template('admin/news_review_detail.html', data={"errmsg": "未查询到此新闻"})

    
    data = {"news": news.to_dict()}
    return render_template('admin/news_review_detail.html', data=data)
```

* 修改待审核列表中 `news_review.html` 审核的链接地址:

```text
<td>
    <a href="{{ url_for('admin.news_review_detail') }}?news_id={{ news.id }}" class="review">审核</a>
</td>
```

* `news_review_detail.html` 数据渲染

```text
<div class="form_group">
    <label>新闻标题：</label>
    <input type="text" class="input_txt2" value="{{ data.news.title }}" readonly>
</div>
<div class="form_group">
        <label>新闻分类：</label>
        <div class="category_name">{{ data.news.category.name }}</div>
</div>
<div class="form_group">
    <label>新闻摘要：</label>
    <textarea class="input_multxt" readonly>{{ data.news.digest }}</textarea>
</div>
<div class="form_group">
    <label>索引图片：</label>
    <img src="{{ data.news.index_image_url }}" alt="索引图片" class="index_pic">
</div>
<div class="form_group">
    <label>新闻内容：</label>
    <div class="rich_wrap fl">
        <input class="input_area" id="rich_content" name="content" value="{{ data.news.content }}"></input>
    </div>
</div>

...

<input name="news_id" value="{{ data.news.id }}" hidden>
```

> 运行测试

## 新闻审核 <a id="&#x65B0;&#x95FB;&#x5BA1;&#x6838;"></a>

* 修改视图函数，使其能够接受POST请求执行审核操作，并实现其代码
  * 因为在审核完成之后需要跳转界面，所以以处需要使用 ajax 进行提交，并审核完成之后使用 js 进行跳转

```text
@admin_blu.route('/news_review_detail', methods=["GET", "POST"])
def news_review_detail():
    """新闻审核"""
    if request.method == "GET":
        ...
        return render_template('admin/news_review_detail.html', data=data)

    

    
    news_id = request.json.get("news_id")
    action = request.json.get("action")

    
    if not all([news_id, action]):
        return jsonify(errno=RET.PARAMERR, errmsg="参数错误")
    if action not in ("accept", "reject"):
        return jsonify(errno=RET.PARAMERR, errmsg="参数错误")

    news = None
    try:
        
        news = News.query.get(news_id)
    except Exception as e:
        current_app.logger.error(e)

    if not news:
        return jsonify(errno=RET.NODATA, errmsg="未查询到数据")

    
    if action == "accept":
        news.status = 0
    else:
        
        reason = request.json.get("reason")
        if not reason:
            return jsonify(errno=RET.PARAMERR, errmsg="参数错误")
        news.reason = reason
        news.status = -1

    
    try:
        db.session.commit()
    except Exception as e:
        current_app.logger.error(e)
        db.session.rollback()
        return jsonify(errno=RET.DBERR, errmsg="数据保存失败")
    return jsonify(errno=RET.OK, errmsg="操作成功")
```

* 在 `static/admin/js/news_review_detail.js` 中添加以下代码

```text
$(".news_review").submit(function (e) {
    e.preventDefault()

    var params = {};
    
    $(this).serializeArray().map(function (x) {
        params[x.name] = x.value;
    });
    
    var action = params["action"];
    var news_id = params["news_id"];
    var reason = params["reason"];
    if (action == "reject" && !reason) {
        alert('请输入拒绝原因');
        return;
    }
    params = {
        "action": action,
        "news_id": news_id,
        "reason": reason
    }
    $.ajax({
        url: "/admin/news_review_detail",
        type: "post",
        contentType: "application/json",
        headers: {
            "X-CSRFToken": getCookie("csrf_token")
        },
        data: JSON.stringify(params),
        success: function (resp) {
            if (resp.errno == "0") {
                
                location.href = document.referrer;
            }else {
                alert(resp.errmsg);
            }
        }
    })
})
```

> 运行测试，注意：在模板界面需要填充隐藏的新闻id字段，以便在提交的时候直接带上该参数

