---
title: Ubuntu 16.04 LTS 部署 readthedocs 文档系统
date: 2019-01-02 16:20:30
tags: readthedocs, ubuntu
---

***Abstract*** : 在 `Ubuntu 16.04 LTS` 中部署 `readthedocs`  文档系统

***Key Words***: Ubuntu, readthedocs

## Intruduction

文档系统在一个软件开发流程中的地位越来越高，而 `readthedocs` 这款文档系统也是这样应运而生。正如其[官方](https://readthedocs.org/)说明：`Read the Docs simplifies software documentation by automating building, versioning, and hosting of your docs for you.` 安装流程大致依照 [官方安装文档](https://docs.readthedocs.io/en/latest/install.html)

## Requirements

* Ubuntu ( version = 16.04.2 LTS )
* Python ( version >= 3.6 )
* Git ( version >= 2.17.0 )
* Pip ( version >= 1.5 )

```bash
sudo apt-get install build-essential
sudo apt-get install python-dev python-pip python-setuptools
sudo apt-get install libxml2-dev libxslt1-dev zlib1g-dev
sudo apt-get install redis-server
```

## Get and Run Read the Docs

### 从 `github` 上获取最新系统

```bash
git clone https://github.com/rtfd/readthedocs.org.git
cd readthedocs.org
```

### 创建虚拟环境并激活

```bash
virtualenv venv
source venv/bin/activate
```

### `Pip` 安装依赖包，类似 `npm install`

```bash
pip install -r requirements.txt
```

这个时间较长，建议不要在这里等待

### 初始化系统

```bash
# 创建数据库
python manage.py migrate
# 创建超级用户
python manage.py createsuperuser
# 初始化静态资源
python manage.py collectstatic
```

### 运行系统

```bash
python manage.py runserver 0.0.0.0:8080
```

每次到这个时候，都应该怀着激动的心情，有一种成败在此一举的感觉。这时就可以用浏览器打开，用之前创建的超级用户登录，这样就能看到大致这样的界面了。

![\images\readthedocs_1](/images/readthedocs_1.jpg)

### 基本配置

修改 `readthedocs/settings/dev.py` 文件，使其可以不用邮箱验证就可以使用本系统

```diff
@@ -9,7 +9,7 @@ class CommunityDevSettings(CommunityBaseSettings):

     """Settings for local development"""

-    PRODUCTION_DOMAIN = 'localhost:8000'
+    PRODUCTION_DOMAIN = '192.168.105.109:8989'
     WEBSOCKET_HOST = 'localhost:8088'

     @property
@@ -23,20 +23,26 @@ class CommunityDevSettings(CommunityBaseSettings):

     DONT_HIT_DB = False

-    ACCOUNT_EMAIL_VERIFICATION = 'none'
     SESSION_COOKIE_DOMAIN = None
     CACHE_BACKEND = 'dummy://'

     SLUMBER_USERNAME = 'test'
     SLUMBER_PASSWORD = 'test'  # noqa: ignore dodgy check
-    SLUMBER_API_HOST = 'http://127.0.0.1:8000'
-    PUBLIC_API_URL = 'http://127.0.0.1:8000'
+    SLUMBER_API_HOST = 'http://192.168.105.109:8989'
+    PUBLIC_API_URL = 'http://localhost:8989'

     BROKER_URL = 'redis://localhost:6379/0'
     CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'
     CELERY_RESULT_SERIALIZER = 'json'
     CELERY_ALWAYS_EAGER = True
-    CELERY_TASK_IGNORE_RESULT = False

+    ACCOUNT_EMAIL_VERIFICATION = 'none'
+
+    HAYSTACK_CONNECTIONS = {
+        'default': {
+            'ENGINE': 'haystack.backends.simple_backend.SimpleEngine',
+        },
+    }
```

## 导入项目生成文档

`readthedocs` 的项目网上已经有了很多的介绍了，这个不再赘述。假设你现在已经拥有了一个这样的项目，并在根目录下有 `conf.py` ，这里重点看一下 `Advanced Settings`。

![\images\readthedocs_3](/images/readthedocs_3.jpg)

上图取消了 `PDF` 和 `EPUB` 的生成，因为这个生成软件并未配置，作为开发文档系统来讲没有必要，提高 `build` 速度。

## Fix Issues

### 更新文档不生效，build 报错

报错如下图：

![\images\readthedocs_2](/images/readthedocs_2.jpg)

这个是 `Git` 版本过低的原因， `Ubuntu 16.04` 默认源里 `Git` 的版本是 `2.7.x`，没有 `--prune-tags` 这个参数导致 `build` 出错，这时我们只需要更新 `Git` 版本即可。

```bash
# 添加 git 源
sudo add-apt-repository ppa:git-core/ppa
# 更新软件列表
sudo apt-get update
# 升级 git
sudo apt-get install git
```

### 代码无法高亮

这个是 `sphinx-rtd-theme` 的 bug，其已在 `0.3` 之后的版本解决，所以通过 `pip show sphinx-rtd-theme` 查看下版本号。如果确实过低了，建议按照上述流程再走一遍。

## Conclusion

整体来讲，部署不算复杂，但其中或多或少有点坑，记录一下，不要再让其他人摸着石头过河。对于一个文档系统，最重要的是内容，现在这样一个简约而不简单的系统恰好符合预期，感谢 `readthedocs` 团队。

## Reference

1. [https://docs.readthedocs.io/en/latest/install.html](https://docs.readthedocs.io/en/latest/install.html)
2. [https://blog.csdn.net/Ezreal_King/article/details/79999131](https://blog.csdn.net/Ezreal_King/article/details/79999131)
3. [https://github.com/rtfd/sphinx_rtd_theme/issues/603](https://github.com/rtfd/sphinx_rtd_theme/issues/603)
4. [https://github.com/rtfd/readthedocs.org/issues/4072](https://github.com/rtfd/readthedocs.org/issues/4072)
