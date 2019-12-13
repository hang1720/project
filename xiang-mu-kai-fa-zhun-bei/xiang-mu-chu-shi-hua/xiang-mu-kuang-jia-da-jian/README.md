# 项目框架搭建 \|

* 目标：
  * 使用 Pycharm 为项目设置 Git 版本控制
  * 完成项目基本配置
  * 抽取代码，熟悉项目目录结构

## 创建项目 <a id="&#x521B;&#x5EFA;&#x9879;&#x76EE;"></a>

* 新建项目，虚拟环境选择python3版本，创建 `manage.py` 文件

```text
from flask import Flask

app = Flask(__name__)

@app.route('/index')
def index():
    return 'index'

if __name__ == '__main__':
    app.run()
```

