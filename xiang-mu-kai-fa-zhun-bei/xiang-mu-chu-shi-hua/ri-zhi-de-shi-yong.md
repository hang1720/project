# 日志的使用 \|

## 日志相关概念 <a id="&#x65E5;&#x5FD7;&#x76F8;&#x5173;&#x6982;&#x5FF5;"></a>

* 日志是一种可以追踪某些软件运行时所发生事件的方法
* 软件开发人员可以向他们的代码中调用日志记录相关的方法来表明发生了某些事情
* 一个事件可以用一个可包含可选变量数据的消息来描述
* 此外，事件也有重要性的概念，这个重要性也可以被称为严重性级别（level）

### 日志的作用 <a id="&#x65E5;&#x5FD7;&#x7684;&#x4F5C;&#x7528;"></a>

* 通过log的分析，可以方便用户了解系统或软件、应用的运行情况；
  * 如果你的应用log足够丰富，也可以分析以往用户的操作行为、类型喜好、地域分布或其他更多信息；
  * 如果一个应用的log同时也分了多个级别，那么可以很轻易地分析得到该应用的健康状况，及时发现问题并快速定位、解决问题，补救损失。
* 简单来讲就是
  * 我们通过记录和分析日志可以了解一个系统或软件程序运行情况是否正常，
  * 也可以在应用程序出现故障时快速定位问题。
  * 不仅在开发中，在运维中日志也很重要
* 日志的作用可以简单总结为以下几点：
  * 程序调试
  * 了解软件程序运行情况，是否正常
  * 软件程序运行故障分析与问题定位
  * 如果应用的日志信息足够详细和丰富，还可以用来做用户行为分析

### 日志的等级 <a id="&#x65E5;&#x5FD7;&#x7684;&#x7B49;&#x7EA7;"></a>

* 我们先来思考下下面的两个问题：
  * 作为开发人员，在开发一个应用程序时需要什么日志信息？在应用程序正式上线后需要什么日志信息？
  * 作为应用运维人员，在部署开发环境时需要什么日志信息？在部署生产环境时需要什么日志信息？
* 在软件开发阶段或部署开发环境时，为了尽可能详细的查看应用程序的运行状态来保证上线后的稳定性，我们可能需要把该应用程序所有的运行日志全部记录下来进行分析，这是非常耗费机器性能的
* 当应用程序正式发布或在生产环境部署应用程序时，我们通常只需要记录应用程序的异常信息、错误信息等，这样既可以减小服务器的I/O压力，也可以更加方便的进行故障排查。
* 那么，怎样才能在不改动应用程序代码的情况下实现在不同的环境记录不同详细程度的日志呢？这就是日志等级的作用了，我们通过配置文件指定我们需要的日志等级就可以了。
* 不同的应用程序所定义的日志等级可能会有所差别，分的详细点的会包含以下几个等级：
  * FATAL/CRITICAL = 重大的，危险的
  * ERROR = 错误
  * WARNING = 警告
  * INFO = 信息
  * DEBUG = 调试
  * NOTSET = 没有设置

### 日志字段信息与日志格式 <a id="&#x65E5;&#x5FD7;&#x5B57;&#x6BB5;&#x4FE1;&#x606F;&#x4E0E;&#x65E5;&#x5FD7;&#x683C;&#x5F0F;"></a>

输出一条日志时，日志内容和日志级别是需要开发人员明确指定的。对于而其它字段信息，只需要是否显示在日志中就可以了。

### 日志功能的实现 <a id="&#x65E5;&#x5FD7;&#x529F;&#x80FD;&#x7684;&#x5B9E;&#x73B0;"></a>

Python 自身提供了一个用于记录日志的标准库模块：logging。

## logging 模块 <a id="logging-&#x6A21;&#x5757;"></a>

* logging 模块定义的函数和类为应用程序和库的开发实现了一个灵活的事件日志系统
* logging 模块是 Python 的一个标准库模块，由标准库模块提供日志记录 API 的关键好处是所有 Python 模块都可以使用这个日志记录功能。

### logging 模块的日志级别 <a id="logging-&#x6A21;&#x5757;&#x7684;&#x65E5;&#x5FD7;&#x7EA7;&#x522B;"></a>

* logging模块默认定义了以下几个日志等级，它允许开发人员自定义其他日志级别，但是这是不被推荐的，尤其是在开发供别人使用的库时，因为这会导致日志级别的混乱。
  * DEBUG 最详细的日志信息，典型应用场景是 问题诊断
  * INFO 信息详细程度仅次于DEBUG，通常只记录关键节点信息，用于确认一切都是按照我们预期的那样进行工作
  * WARNING 当某些不期望的事情发生时记录的信息（如，磁盘可用空间较低），但是此时应用程序还是正常运行的
  * ERROR 由于一个更严重的问题导致某些功能不能正常运行时记录的信息
  * FATAL/CRITICAL 整个系统即将/完全崩溃
* 开发应用程序或部署开发环境时，可以使用 DEBUG 或 INFO 级别的日志获取尽可能详细的日志信息来进行开发或部署调试；
* 应用上线或部署生产环境时，应该使用 WARNING 或 ERROR 或 CRITICAL 级别的日志来降低机器的I/O压力和提高获取错误日志信息的效率。

> 日志级别的指定通常都是在应用程序的配置文件中进行指定的。

### logging 模块的使用方式介绍 <a id="logging-&#x6A21;&#x5757;&#x7684;&#x4F7F;&#x7528;&#x65B9;&#x5F0F;&#x4ECB;&#x7ECD;"></a>

* loggers 提供应用程序代码直接使用的接口
* handlers 用于将日志记录发送到指定的目的位置
* filters 提供更细粒度的日志过滤功能，用于决定哪些日志记录将会被输出（其它的日志记录将会被忽略）
* formatters 用于控制日志信息的最终输出格式

```text

logging.basicConfig(level=logging.DEBUG) 

file_log_handler = RotatingFileHandler("logs/log", maxBytes=1024*1024*100, backupCount=10)

formatter = logging.Formatter('%(levelname)s %(filename)s:%(lineno)d %(message)s')

file_log_handler.setFormatter(formatter)

logging.getLogger().addHandler(file_log_handler)
```

## 使用logging提供的模块级别的函数记录日志 <a id="&#x4F7F;&#x7528;logging&#x63D0;&#x4F9B;&#x7684;&#x6A21;&#x5757;&#x7EA7;&#x522B;&#x7684;&#x51FD;&#x6570;&#x8BB0;&#x5F55;&#x65E5;&#x5FD7;"></a>

### 最简单的日志输出 <a id="&#x6700;&#x7B80;&#x5355;&#x7684;&#x65E5;&#x5FD7;&#x8F93;&#x51FA;"></a>

* 先来试着分别输出一条不同日志级别的日志记录：

```text
import logging

logging.debug("This is a debug log.")
logging.info("This is a info log.")
logging.warning("This is a warning log.")
logging.error("This is a error log.")
logging.critical("This is a critical log.")
```

* 也可以这样写：

```text
logging.log(logging.DEBUG, "This is a debug log.")
logging.log(logging.INFO, "This is a info log.")
logging.log(logging.WARNING, "This is a warning log.")
logging.log(logging.ERROR, "This is a error log.")
logging.log(logging.CRITICAL, "This is a critical log.")
```

### 修改配置改变输出内容 <a id="&#x4FEE;&#x6539;&#x914D;&#x7F6E;&#x6539;&#x53D8;&#x8F93;&#x51FA;&#x5185;&#x5BB9;"></a>

```text
logging.basicConfig(level=logging.DEBUG)
```

> 切记：设置 `Configurations` 中的 **Working directory** 为当前项目

## 集成日志到当前项目 <a id="&#x96C6;&#x6210;&#x65E5;&#x5FD7;&#x5230;&#x5F53;&#x524D;&#x9879;&#x76EE;"></a>

* 在 `config.py` 文件中在不同的环境的配置下添加日志级别

```text
class Config(object):
    ...

    
    LOG_LEVEL = logging.DEBUG


class ProductionConfig(Config):
    """生产模式下的配置"""
    LOG_LEVEL = logging.ERROR
```

* 在 `info` 目录下的 `init.py` 文件中添加日志配置的相关方法

```text
def setup_log(config_name):
    """配置日志"""

    
    logging.basicConfig(level=config[config_name].LOG_LEVEL)  
    
    file_log_handler = RotatingFileHandler("logs/log", maxBytes=1024 * 1024 * 100, backupCount=10)
    
    formatter = logging.Formatter('%(levelname)s %(filename)s:%(lineno)d %(message)s')
    
    file_log_handler.setFormatter(formatter)
    
    logging.getLogger().addHandler(file_log_handler)
```

* 在 `create_app` 方法中调用上一步创建的方法，并传入 `config_name`

```text
def create_app(config_name):
    ...

    
    setup_log(config_name)
    app = Flask(__name__)
    ...
```

* 在项目根目录下创建日志目录文件夹 `logs`，如下：

> 运行项目，当前项目日志已输出到 `logs` 的目录下自动创建的 log 文件中

* 在 logs 文件夹下创建 .gitkeep 文件，以便能将 logs 文件夹添加到远程仓库，并在 .gitignore 文件中添加忽略提交生成的日志文件

```text
logs/log*
```

在 Flask框架 中，其自己对 Python 的 logging 进行了封装，在 Flask 应用程序中，可以以如下方式进行输出 log:

```text
current_app.logger.debug('debug')
current_app.logger.error('error')
```

> 当前应用程序的 logger 会根据应用程序的调试状态去调整日志级别，如下图：

