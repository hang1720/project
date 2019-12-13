# 基本资料设置 \|

## 需求分析 <a id="&#x9700;&#x6C42;&#x5206;&#x6790;"></a>

* 进入界面之后展示用户的基本资料\(个性签名、昵称、性别\)
* 用户修改之后点击保存向服务器发起请求进行数据保存更新

## 默认数据展示 <a id="&#x9ED8;&#x8BA4;&#x6570;&#x636E;&#x5C55;&#x793A;"></a>

* 将 `static/news/user_base_info.html` 拖动到模板文件夹下的 `news` 文件夹内，并引入css：

```text
<link rel="stylesheet" type="text/css" href="../../static/news/css/reset.css">
<link rel="stylesheet" type="text/css" href="../../static/news/css/main.css">
```

* 在 `profile/views.py` 文件中添加路由函数，用于在展示界面时填上默认数据

```text
@profile_blu.route('/user_info')
@user_login_data
def base_info():
    """
    用户基本信息
    :return:
    """
    return render_template('news/user_base_info.html', data={"user_info": user.to_dict()})
```

* 修改 `news/user.html` 中用户基本信息的链接

```text
<li class="active"><a href="{{ url_for("profile.base_info") }}" target="main_frame">基本资料</a></li>
```

* 将模板中相关数据进行填充

```text
<form class="base_info">
    <h3>基本资料</h3>
    <div class="form-group">
        <label>个性签名：</label>
        <input type="text" id="signature" name="signature" class="input_txt" value="{{ data.user_info.signature }}">
    </div>
    <div class="form-group">
        <label>用户昵称：</label>
        <input type="text" id="nick_name" name="nick_name" class="input_txt" value="{{ data.user_info.nick_name }}">
    </div>
    <div class="form-group">
        <label>性别：</label>
        {% if data.user_info.gender == "MAN" %}
            <input class="gender" type="radio" name="gender" checked="checked" value="MAN"> <b>男</b>
            <input class="gender" class="gender" type="radio" name="gender" value="WOMAN">  <b>女</b>
        {% else %}
            <input class="gender" type="radio" name="gender" value="MAN"> <b>男</b>&nbsp;&nbsp;&nbsp;
            <input class="gender" type="radio" name="gender" value="WOMAN" checked="checked">  <b>女</b>
        {% endif %}
    </div>
    <div class="form-group">
        <input type="submit" value="保 存" class="input_sub">
    </div>
</form>
```

