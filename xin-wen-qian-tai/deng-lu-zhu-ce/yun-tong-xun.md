# 云通讯 \|

## 云通讯 <a id="&#x4E91;&#x901A;&#x8BAF;"></a>

* 用于发送短信\(或语音\)验证码的第三方平台
* 云通讯：[http://www.yuntongxun.com/](http://www.yuntongxun.com/)
* 登录注册+实名认证
* 文档及Demo地址：[http://www.yuntongxun.com/doc/ready/demo/1\_4\_1\_2.html](http://www.yuntongxun.com/doc/ready/demo/1_4_1_2.html)
* 云通讯相关参数
* 设置测试号码

## 集成到项目 <a id="&#x96C6;&#x6210;&#x5230;&#x9879;&#x76EE;"></a>

* 将从项目资料文件中的 `CCPRestSDK.py` 和 `xmltojson.py` 拷贝至 `info\lib\yuntongxun` 目录下

```text
from ihome.libs.yuntongxun.CCPRestSDK import REST


accountSid = 'xxxxxx'


accountToken = 'xxxxxx'


appId = 'xxxxxxx'


serverIP = 'sandboxapp.cloopen.com'


serverPort = '8883'


softVersion = '2013-12-26'
```

* 创建CCP的单例实例

```text

class CCP(object):
    """发送短信的辅助类"""

    def __new__(cls, *args, **kwargs):
        
        if not hasattr(CCP, "instance"):
            cls.instance = super(CCP, cls).__new__(cls, *args, **kwargs)
            cls.instance.rest = REST(serverIP, serverPort, softVersion)
            cls.instance.rest.setAccount(accountSid, accountToken)
            cls.instance.rest.setAppId(appId)
        return cls.instance

    def send_template_sms(self, to, datas, temp_id):
        """发送模板短信"""
        
        
        
        result = self.rest.sendTemplateSMS(to, datas, temp_id)
        
        if result.get("statusCode") == "000000":
            
            return 0
        else:
            
            return -1
```

> 类似的短信平台有：极光、阿里云等等

