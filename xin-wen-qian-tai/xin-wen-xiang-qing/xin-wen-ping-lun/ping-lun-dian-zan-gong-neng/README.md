# 评论点赞功能 \|

## 需求分析 <a id="&#x9700;&#x6C42;&#x5206;&#x6790;"></a>

* 后端提供点赞和取消点赞功能
* 当用户点击未点赞按钮，执行点赞逻辑，向后端发起点赞请求，取消点赞则反之
* 在新闻显示完成之后，底部评论会根据当前登录用户显示是否点赞图标

## 接口设计 <a id="&#x63A5;&#x53E3;&#x8BBE;&#x8BA1;"></a>

* URL：/news/comment\_like
* 请求方式：POST
* 传入参数：JSON格式
* 参数

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| comment\_id | int | 是 | 评论id |
| news\_id | int | 是 | 新闻id |
| action | string | 是 | 点赞操作类型：add\(点赞\)，remove\(取消点赞\) |

* 返回类型：JSON

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| errno | int | 是 | 错误码 |
| errmsg | string | 是 | 错误信息 |

## 后端代码实现 <a id="&#x540E;&#x7AEF;&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 在 `news/views.py` 中添加点赞/取消点赞视图函数

```text
@news_blu.route('/comment_like', methods=["POST"])
@user_login_data
def set_comment_like():
    """评论点赞"""

    if not g.user:
        return jsonify(errno=RET.SESSIONERR, errmsg="用户未登录")

    
    comment_id = request.json.get("comment_id")
    news_id = request.json.get("news_id")
    action = request.json.get("action")

    
    if not all([comment_id, news_id, action]):
        return jsonify(errno=RET.PARAMERR, errmsg="参数错误")

    if action not in ("add", "remove"):
        return jsonify(errno=RET.PARAMERR, errmsg="参数错误")

    
    try:
        comment = Comment.query.get(comment_id)
    except Exception as e:
        current_app.logger.error(e)
        return jsonify(errno=RET.DBERR, errmsg="查询数据失败")

    if not comment:
        return jsonify(errno=RET.NODATA, errmsg="评论数据不存在")

    if action == "add":
        comment_like = CommentLike.query.filter_by(comment_id=comment_id, user_id=g.user.id).first()
        if not comment_like:
            comment_like = CommentLike()
            comment_like.comment_id = comment_id
            comment_like.user_id = g.user.id
            db.session.add(comment_like)
            
            comment.like_count += 1
    else:
        
        comment_like = CommentLike.query.filter_by(comment_id=comment_id, user_id=g.user.id).first()
        if comment_like:
            db.session.delete(comment_like)
            
            comment.like_count -= 1

    try:
        db.session.commit()
    except Exception as e:
        current_app.logger.error(e)
        db.session.rollback()
        return jsonify(errno=RET.DBERR, errmsg="操作失败")
    return jsonify(errno=RET.OK, errmsg="操作成功")
```

