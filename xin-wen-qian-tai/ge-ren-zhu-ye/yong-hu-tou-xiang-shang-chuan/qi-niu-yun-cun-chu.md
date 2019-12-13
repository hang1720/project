# 七牛云存储 \|

* 对于实际项目的作用：
  * 用于在实际项目中存储媒体\(图像、音频、视频\)文件
  * 节省自己服务器空间，节约宽带，提升媒体文件访问的稳定性
  * 不需要人力物力对重复数据、冗余数据进行清理及判断
* 官网：[https://www.qiniu.com/](https://www.qiniu.com/)
* SDK地址：[https://developer.qiniu.com/sdk\#official-sdk](https://developer.qiniu.com/sdk#official-sdk)
* 注册，登录，**实名认证**
* 创建存储空间
* 拷贝外链默认域名到项目中
* 拷贝 access\_key 和 secret\_key 到项目中

## 封装上传图片工具类 <a id="&#x5C01;&#x88C5;&#x4E0A;&#x4F20;&#x56FE;&#x7247;&#x5DE5;&#x5177;&#x7C7B;"></a>

* 安装工具包

```text
pip install qiniu
```

* 在 `info/utils` 目录下创建 `image_storage.py` 文件
* `image_storage`示例代码：

```text


import logging

from qiniu import Auth, put_data


access_key = 'yV4GmNBLOgQK-1Sn3o4jktGLFdFSrlywR2C-hvsW'
secret_key = 'bixMURPL6tHjrb8QKVg2tm7n9k8C7vaOeQ4MEoeW'


bucket_name = 'ihome'


def storage(data):
    """七牛云存储上传文件接口"""
    if not data:
        return None
    try:
        
        q = Auth(access_key, secret_key)

        
        token = q.upload_token(bucket_name)

        
        ret, info = put_data(token, None, data)

    except Exception as e:
        logging.error(e)
        raise e

    if info and info.status_code != 200:
        raise Exception("上传文件到七牛失败")

    
    return ret["key"]


if __name__ == '__main__':
    file_name = input("输入上传的文件")
    with open(file_name, "rb") as f:
        storage(f.read())
```

> 七牛是通过 pip 安装的

