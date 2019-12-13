# 用户头像上传 \|

## 需求分析 <a id="&#x9700;&#x6C42;&#x5206;&#x6790;"></a>

* 用户可以进行头像的修改，上传完毕之后更新个人中心跟头像相关的标签内容
* 上传的头像文件保存到【七牛云】提供的文件存储服务器中

## 模板渲染 <a id="&#x6A21;&#x677F;&#x6E32;&#x67D3;"></a>

* 将 `static/news/user_pic_info.html` 拖动到模板文件夹下的 `news` 文件夹内
* 在 `profile/views.py` 中添加视图函数

```text
@profile_blu.route('/pic_info', methods=["GET", "POST"])
@user_login_data
def pic_info():
    user = g.user
    return render_template('news/user_pic_info.html', data={"user_info": user.to_dict()})
```

* 在 `user_pic_info.html` 中设置模板语言

```text
<div class="form-group">
    <label class="label01">当前图像：</label>
    <img src="{% if data.user_info.avatar_url %}{{ data.user_info.avatar_url }}{% else %}../../static/news/images/user_pic.png{% endif %}" alt="用户图片" class="now_user_pic">
</div>
```

* 修改 `news/user.html` 中用户头像设置的链接

```text
<li><a href="/user/pic_info" target="main_frame">头像设置</a></li>
```

