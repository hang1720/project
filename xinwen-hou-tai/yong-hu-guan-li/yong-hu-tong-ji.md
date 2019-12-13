# 用户统计 \|

## 需求分析 <a id="&#x9700;&#x6C42;&#x5206;&#x6790;"></a>

* 展示当前总人数，月活跃人数，日活跃人数
* 使用图表的形式展示活跃曲线

## 实现准备 <a id="&#x5B9E;&#x73B0;&#x51C6;&#x5907;"></a>

* 将 `static/admin/user_count.html` 拖到 `templates/admin/` 目录下

## 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

* 实现思路：
  * 月新增数：获取到本月第1天0点0分0秒的时间对象，然后查询最后一次登录比其大的所有数据
  * 日新增数：获取到当日0点0分0秒时间对象，然后查询最后一次登录比其大的所有数据
  * 图表查询：遍历查询数据每一天的数据\(当前天数，减去某些天\)
* 后端查询数据实现

```text
@admin_blu.route('/user_count')
def user_count():
    
    total_count = 0
    try:
        total_count = User.query.filter(User.is_admin == False).count()
    except Exception as e:
        current_app.logger.error(e)

    
    mon_count = 0
    try:
        now = time.localtime()
        mon_begin = '%d-%02d-01' % (now.tm_year, now.tm_mon)
        mon_begin_date = datetime.strptime(mon_begin, '%Y-%m-%d')
        mon_count = User.query.filter(User.is_admin == False, User.create_time >= mon_begin_date).count()
    except Exception as e:
        current_app.logger.error(e)

    
    day_count = 0
    try:
        day_begin = '%d-%02d-%02d' % (now.tm_year, now.tm_mon, now.tm_mday)
        day_begin_date = datetime.strptime(day_begin, '%Y-%m-%d')
        day_count = User.query.filter(User.is_admin == False, User.create_time > day_begin_date).count()
    except Exception as e:
        current_app.logger.error(e)

    
    

    now_date = datetime.strptime(datetime.now().strftime('%Y-%m-%d'), '%Y-%m-%d')
    
    active_date = []
    active_count = []

    
    for i in range(0, 31):
        begin_date = now_date - timedelta(days=i)
        end_date = now_date - timedelta(days=(i - 1))
        active_date.append(begin_date.strftime('%Y-%m-%d'))
        count = 0
        try:
            count = User.query.filter(User.is_admin == False, User.last_login >= day_begin,
                                      User.last_login < day_end).count()
        except Exception as e:
            current_app.logger.error(e)
        active_count.append(count)

    active_date.reverse()
    active_count.reverse()

    data = {"total_count": total_count, "mon_count": mon_count, "day_count": day_count, "active_date": active_date,
            "active_count": active_count}

    return render_template('admin/user_count.html', data=data)
```

* 修改 `templates/admin/index.html` 中【用户统计】链接

```text
<li><a class="icon022" href="/admin/user_count" target="main_frame">用户统计</a></li>
```

* 前端页面渲染数据实现

```text
<div class="spannels">
    <div class="spannel scolor01">
        <em>{{ data.total_count }}</em><span>人</span>
        <b>用户总数</b>
    </div>
    <div class="spannel scolor02">
        <em>{{ data.mon_count }}</em><span>人</span>
        <b>用户月新增数</b>
    </div>
    <div class="spannel2 scolor03">
        <em>{{ data.day_count }}</em><span>人</span>
        <b>用户日新增数</b>
    </div>
</div>
...


xAxis : [
    {
        name: '今天',
        type : 'category',
        boundaryGap : false,
        data : {{ data.active_date | safe }}
    }
],        
yAxis : [
    {
        name: '活跃用户数量',
        type : 'value'
    }
],      
series : [
    {
        name:'active',
        type:'line',
        smooth:true,
        itemStyle: {normal: {areaStyle: {type: 'default'}, color: '#f80'}, lineStyle: {color: '#f80'}},
        data: {{ data.active_count }}
    }],
```

> 运行测试

