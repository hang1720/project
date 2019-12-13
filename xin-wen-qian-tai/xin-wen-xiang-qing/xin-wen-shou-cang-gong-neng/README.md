# 新闻收藏功能 \|

## 需求分析： <a id="&#x9700;&#x6C42;&#x5206;&#x6790;&#xFF1A;"></a>

* 进入到新闻详情页之后，如果用户已收藏该新闻，则显示已收藏，点击则为取消收藏，反之点击收藏该新闻
* 因为只更新界面上部分元素，所以收藏和取消收藏的逻辑

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

代码实现分为以下几步：

* 前端根据后台返回数据显示收藏或者取消收藏按钮
* 后端提供收藏与取消收藏接口
* 前端发起收藏或者取消收藏请求

## 前端是否收藏界面展示 <a id="&#x524D;&#x7AEF;&#x662F;&#x5426;&#x6536;&#x85CF;&#x754C;&#x9762;&#x5C55;&#x793A;"></a>

* 在新闻详情的视图函数中添加变量 `is_collected`，并在渲染模板时传入

```text
@news_blu.route('/<int:news_id>')
@user_login_data
def news_detail(news_id):
    ...
    
    is_collected = False
    
    if g.user:
        if news in g.user.collection_news:
            is_collected = True
    data = {
        "news": news.to_dict(),
        "click_news_list": click_news_list,
        "is_collected": is_collected,
        "user_info": g.user.to_dict() if g.user else None,
    }
    return render_template('news/detail.html', data=data)
```

* 前端根据传入值判断显示哪一个a标签，并使用标签记录当前新闻id，以供后续逻辑使用

```text
    <a href="javascript:;" class="collected block-center" data-newid="{{ data.news.id }}" style="display: {% if data.is_collected %} block
    {% else %} none {% endif %};"><span class="out">已收藏</span><span class="over">取消收藏</span></a>
    <a href="javascript:;" class="collection block-center" data-newid="{{ data.news.id }}" style="display: {% if data.is_collected %} none
            {% else %} block {% endif %};">收藏</a>
```

