# 后端实现 \|

* 在 `index/views.py` 中定义视图函数
  * 在查询的时候，如果用户分类id传0，则不添加分类查询条件

```text
@index_blu.route('/newslist')
def get_news_list():
    """
    获取指定分类的新闻列表
    1. 获取参数
    2. 校验参数
    3. 查询数据
    4. 返回数据
    :return:
    """

    
    args_dict = request.args
    page = args_dict.get("p", '1')
    per_page = args_dict.get("per_page", constants.HOME_PAGE_MAX_NEWS)
    category_id = args_dict.get("cid", '1')

    
    try:
        page = int(page)
        per_page = int(per_page)
    except Exception as e:
        current_app.logger.error(e)
        return jsonify(errno=RET.PARAMERR, errmsg="参数错误")

    
    filters = []
    
    if category_id != "1":
        filters.append(News.category_id == category_id)
    try:
        paginate = News.query.filter(*filters).order_by(News.create_time.desc()).paginate(page, per_page, False)
        
        items = paginate.items
        
        total_page = paginate.pages
        current_page = paginate.page
    except Exception as e:
        current_app.logger.error(e)
        return jsonify(errno=RET.DBERR, errmsg="数据查询失败")

    news_li = []
    for news in items:
        news_li.append(news.to_basic_dict())

    
    return jsonify(errno=RET.OK, errmsg="OK", totalPage=total_page, currentPage=current_page, newsList=news_li, cid=category_id)
```

