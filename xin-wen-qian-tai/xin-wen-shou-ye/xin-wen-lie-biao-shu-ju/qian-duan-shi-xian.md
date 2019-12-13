# 前端实现 \|

* 在 `index.js` 文件中，实现 `updateNewsData` 方法

```text
function updateNewsData() {
    var params = {
        "page": 1,
        "cid": currentCid,
        'per_page': 50
    }
    $.get("/newslist", params, function (resp) {
        if (resp) {
            
            $(".list_con").html('')
            
            for (var i=0;i<resp.newsList.length;i++) {
                var news = resp.newsList[i]
                var content = '<li>'
                content += '<a href="#" class="news_pic fl"><img src="' + news.index_image_url + '?imageView2/1/w/170/h/170"></a>'
                content += '<a href="#" class="news_title fl">' + news.title + '</a>'
                content += '<a href="#" class="news_detail fl">' + news.digest + '</a>'
                content += '<div class="author_info fl">'
                content += '<div class="source fl">来源：' + news.source + '</div>'
                content += '<div class="time fl">' + news.create_time + '</div>'
                content += '</div>'
                content += '</li>'
                $(".list_con").append(content)
            }
        }
    })
}
```

* 并在界面加载完成之后，首次加载新闻列表数据

```text
$(function () {
    
    updateNewsData()
    ...
})
```

> 运行测试，新闻数据正常展示

* 实现分页效果-No.1

```text

$(window).scroll(function () {
    
    var showHeight = $(window).height();
    
    var pageHeight = $(document).height();
    
    var canScrollHeight = pageHeight - showHeight;
    
    var nowScroll = $(document).scrollTop();
    if ((canScrollHeight - nowScroll) < 100) {
        if (!house_data_querying) {
            
            house_data_querying = true;
            
            if(cur_page < total_page) {
                
                updateNewsData();
            } else {
                house_data_querying = false;
            }
        }
    }
})
```

* 实现分页效果-No.2

```text
function updateNewsData() {
    var params = {
        "page": cur_page,
        "cid": currentCid,
        'per_page': 50
    }
    $.get("/newslist", params, function (resp) {
        
        data_querying = false
        if (resp) {
            
            total_page = resp.totalPage
            
            if (cur_page == 1) {
                $(".list_con").html('')
            }
            
            cur_page += 1
            for (var i=0;i<resp.newsList.length;i++) {
                ...
            }
        }
    })
}
```

> 运行测试

* 实现切换分类刷新逻辑

```text

$('.menu li').click(function () {
    var clickCid = $(this).attr('data-cid')
    $('.menu li').each(function () {
        $(this).removeClass('active')
    })
    $(this).addClass('active')

    if (clickCid != currentCid) {
        
        
        currentCid = clickCid

        
        cur_page = 1
        total_page = 1
        data_querying = false
        updateNewsData()
    }
})
```

> 运行测试

