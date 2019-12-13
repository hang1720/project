# 数据库表分析及创建 \|

## 数据库表分析及创建 <a id="&#x6570;&#x636E;&#x5E93;&#x8868;&#x5206;&#x6790;&#x53CA;&#x521B;&#x5EFA;"></a>

## 数据库表迁移 <a id="&#x6570;&#x636E;&#x5E93;&#x8868;&#x8FC1;&#x79FB;"></a>

* 确认当前配置的数据库是否存在

```text
mysql> use information;
```

* 将 `constants.py` 和 `models.py` 文件拷贝到项目的 `info` 目录下

> 注：constants.py 是当前项目中要使用的一些常量，预先定义好的，models.py 文件中需要使用到该文件中的一些常量

* 并在 `manage.py` 中导入 `models`

```text

form info import models
```

> 在迁移的时候以便能读取到对应模型

* 执行数据库迁移

```text
$ python manage.py db init
$ python manage.py db migrate -m"initial"
$ python manage.py db upgrade
```

* 查看数据库表是否创建完成

```text
mysql> show tables;
```

* 执行导入初始分类的 SQL 语句

```text
mysql> source info_info_category.sql
```

> 注意：生成的迁移文件不需要提交到 git 保存，所以需要在 .gitignore 文件中添加以下内容以便忽略迁移所生成的系列文件：

```text
migrations
```

## 测试数据的添加 <a id="&#x6D4B;&#x8BD5;&#x6570;&#x636E;&#x7684;&#x6DFB;&#x52A0;"></a>

* 先添加分类测试数据

```text
mysql> source 路径/information_info_category.sql
```

* 再添加新闻测试数据

```text
mysql> source 路径/information_info_news.sql
```

