# 新闻列表数据 \|

* 新闻列表数据只是当前页面的一部分
* 点击分类时需要去获取当前分类下的新闻数据
* 并在展示的时候需要更新新闻列表界面，不需要整体页面刷新
* 所以新闻数据也使用 ajax 的方式去请求后台接口进行获取

## 接口设计 <a id="&#x63A5;&#x53E3;&#x8BBE;&#x8BA1;"></a>

* URL：/news\_list
* 请求方式：GET
* 传入参数：JSON格式
* 参数

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| cid | string | 是 | 分类id |
| page | int | 否 | 页数，不传即获取第1页 |
| per\_page | int | 否 | 每页多少条数据，如果不传，默认10条 |

* 返回类型：JSON

| 参数名 | 类型 | 是否必须 | 参数说明 |
| :--- | :--- | :--- | :--- |
| errno | int | 是 | 错误码 |
| errmsg | string | 是 | 错误信息 |
| cid | string | 是 | 当前新闻数据的分类id |
| totalPage | int | 否 | 总页数 |
| currentPage | int | 否 | 当前页数 |
| newsList | list | 否 | 新闻列表数据 |
| newsList.title | string | 是 | 新闻标题 |
| newsList.source | string | 是 | 新闻来源 |
| newsList.digest | string | 是 | 新闻摘要 |
| newsList.create\_time | string | 是 | 新闻时间 |
| newsList.index\_image\_url | string | 是 | 新闻索引图 |

* 返回示例：

```text
{
    "cid": "0",
    "currentPage": 1,
    "errmsg": "OK",
    "errno": "0",
    "newsList": [
        {
            "clicks": 105,
            "create_time": "2018-01-17 17:00:41",
            "digest": "一周前，新京报获悉，滴滴已在杭州成立代号为“黑马”的事业部，主攻共享电单车和电动汽车，并已经在杭州小范围内测。共享电单车的发布也为滴滴在中短途出行版图中填补了空缺。",
            "id": 1168,
            "index_image_url": "https://wpimg.wallstcn.com/aaabf95b-610d-4548-afbb-7cb13fa2f85a.jpg",
            "source": "张超",
            "status": 0,
            "title": "滴滴被曝电单车起名“街兔” 需缴押金99元"
        },
        {
            "clicks": 40,
            "create_time": "2018-01-17 16:41:46",
            "digest": "绿光资本四季度买进推特、时代华纳、海上钻井平台运营商ESV，其称推特改善了用户体验，2018收入有望再增。绿光资本去年全年投资回报仅有1.6%，远远落后于同期标普21.8%的涨幅。",
            "id": 1165,
            "index_image_url": "https://wpimg.wallstcn.com/d5bd842c-0026-4d69-a620-4ebf6ad7317d.jpg",
            "source": "陶旖洁",
            "status": 0,
            "title": "绿光资本致股东信：今年Twitter会是大赢家"
        }
    ],
    "totalPage": 116
}
```

