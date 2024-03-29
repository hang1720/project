# 新闻首页 \|

## 学习目标 <a id="&#x5B66;&#x4E60;&#x76EE;&#x6807;"></a>

* 能够参照课件步骤使用代码实现新闻点击排行后端逻辑
* 能够参照课件步骤使用代码实现新闻列表后端数据查询逻辑
* 能够写出SQLAlchemy的分页查询获取数据以及总页数的相关代码
* 能够说出上拉加载更多的代码实现步骤

## 需求： <a id="&#x9700;&#x6C42;&#xFF1A;"></a>

* 中间展示新闻分类信息
* 右侧显示新闻点击排行
* 根据分类进行新闻列表展示
* 上拉加载更多数据
* 点击新窗口跳转到新闻详情页

## 重点功能分析 <a id="&#x91CD;&#x70B9;&#x529F;&#x80FD;&#x5206;&#x6790;"></a>

* 因为新闻列表页需要实现上拉加载更多的交互逻辑
* 在用户上拉之后，新增的新闻数据直接拼接到页面的最下方，无需要更新整个页面
* 所以新闻数据的展示不能采用渲染模板的形式
* 需要使用 ajax 请求，请求完毕之后拼接界面元素

