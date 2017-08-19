danmu-server
================
[![David deps](https://david-dm.org/zsxsoft/danmu-server.svg)](https://david-dm.org/zsxsoft/danmu-server)

弹幕服务器，其客户端项目见[danmu-client](https://github.com/zsxsoft/danmu-client)。

**欲使用此项目，客户端需要使用对应的版本。[已发布的服务端](https://github.com/zsxsoft/danmu-server/releases)均已写明对应的客户端版本号，开发分支内的服务端版本仅对应开发分支的客户端。**

## 功能特色
- 跨平台；
- 房间功能；
- 后台管理；
- 弹幕记录与搜索（需要开启数据库）；
- 黑名单功能；
- 关键词替换、拦截功能；
- 弹幕记录；
- 扩展；
   - 新浪微博登录扩展（需要开启缓存）；
   - 自动封禁功能扩展（需要开启缓存）；
   - 审核扩展；
   - 直播拉取扩展
- 删除单条弹幕功能；
- 易于部署，简单高效。

## 后台截图
![后台截图](http://zsxsoft.github.io/danmu-server/management.png)

## 一些警告

稳定版请于[Release](https://github.com/zsxsoft/danmu-server/releases)手动下载。


## 部署方式

### 检查环境

#### Nodejs

Nodejs >= 6

#### 数据库
 如使用``csv``，可无视此节。

 默认使用``MySQL``数据库。如需使用，需检查[MariaDB](https://mariadb.org/)或[MySQL](https://www.mysql.com/)的安装状态。支持``5.0+``。安装完成后，请创建相应的数据库。

 如使用``MongoDB``数据库，请检查[MongoDB](https://www.mongodb.org/)的安装状态。然后需要在安装完成后执行：``npm install mongodb``。

#### 缓存

 如不使用新浪微博与自动封禁功能，可无视此节。

 默认使用``memcached``。如需使用，请检查[Memcached(Linux)](http://memcached.org/)的安装状态。``Windows``用户请自行查找适合的``Memcached``版本。

 如果要用[阿里云开放缓存服务OCS](http://www.aliyun.com/product/ocs/)，需要在安装完成后执行：``npm install aliyun-sdk``。

### 直接安装
 1. 配置MariaDB，创建数据库等，不需要创建数据表。
 2. 修改``config.js``，使其参数与环境相符。
 3. 切换到命令行或终端，``cd``到程序所在目录执行``npm install``，安装程序依赖库。
 4. 现在，你可以直接``npm start``启动。

### Docker安装

__Dockerfile可能年久失修，建议自己用``alpine``封装一个。__
直接用``Docker``安装的话，镜像内是含``MariaDB``的。
 1. [安装Docker](http://yeasy.gitbooks.io/docker_practice/content/install/index.html)。
 2. ``config.js``调整配置。
 3. ``docker build -t="zsxsoft/danmu-server:" . && docker run -t -i -p 3000:3000 "zsxsoft/danmu-server"``

## 升级
### 1.0.6 -> 1.1.0
* 在每个房间内增加cdn: false配置
### 1.0.5 -> 1.0.6
* 在每个房间内增加hostname配置，类型为数组，用于将房间与域名绑定

## 网页接口

### GET /
可以直接发布最简单的弹幕。

### GET /advanced
可以发布高级弹幕（需要密码）

### GET /manage
可以进行后台管理

### GET /realtime
可以实时接收弹幕并直接删除或封禁（需要密码）

## 配置说明

以下标有``*``的配置项，运行时不可在后台修改。

```javascript
	"rooms": {
		"房间1": {
			* "hostname": ["test.zsxsoft.com", "localhost", "127.0.0.1"],
			* "cdn": 是否使用CDN或反向代理（用于获取正确的IP）,
			* "display": "房间显示名",
			* "table": "对应MySQL的数据表、MongoDB的集合",
			"connectpassword": "客户端连接密码",
			"managepassword": "管理密码",
			"advancedpassword": "高级弹幕密码",
			"keyword": {
				"block": /强制屏蔽关键词，正则格式。/
				"replacement": /替换关键词，正则格式/,
				"ignore": /忽略词，正则格式/
			},
			"blockusers": [
				"默认封禁用户列表"
			],
			"maxlength": 弹幕堆积队列最大长度,
			"textlength": 每条弹幕最大长度,
			* "image": {
				* "regex": /图片弹幕解析正则，正则格式，不要修改/ig,
				* "lifetime": 每个图片给每条弹幕增加的存货时间
			},
			"permissions": { // 普通用户允许的弹幕权限
				"send": 弹幕开关；关闭后无论普通用户还是高级权限都完全禁止弹幕。,
				"style": 弹幕样式开关,
				"color": 颜色开关,
				"textStyle": CSS开关,
				"height": 高度开关,
				"lifeTime": 显示时间开关,
			}
		},
		"房间ID2": {
			// 同上
		}
	},
	* "database": { // 数据库
		* "type": "数据库类型（mysql / mongo / csv / none）",
		* "server": " 数据库地址（mysql / mongo）",
		* "username": "数据库用户名（mysql / mongo）",
		* "password": "数据库密码（mysql / mongo）",
		* "port": "数据库端口（mysql / mongo）",
		* "db": "数据库（mysql / mongo）",
		* "retry": 24小时允许断线重连最大次数，超过则自动退出程序。24小时以第一次断线时间计。（mysql）,
		* "timeout": 数据库重连延时及Ping（mysql）,
		* "savedir": "指定文件保存位置（csv）",
	},
	"websocket": {
		"interval": 弹幕发送间隔
		"singlesize": 每次弹幕发送数量
	},
	* "http": {
		* "port": 服务器HTTP端口,
		* "headers": {}, // HTTP头
		* "sessionKey": "随便写点，防冲突的"
	},
	* "cache": {
		* "type": "缓存类型（memcached / aliyun）",
		* "host": "缓存服务器地址，可用socket",
		* "auth": 打开身份验证,
		* "authUser": 身份验证账号,
		* "authPassword": 身份验证密码,
	},
	"ext": {
		// 扩展
	}
}
```

## 扩展
### 新浪微博登录
```javascript
"weibo": { // 新浪微博扩展
	"clientID": '', // App ID
	"clientSecret": '', // App Secret
	"callbackURL": 'http://test.zsxsoft.com:3000/auth/sina/callback', // 这里填写的是 网站地址/auth/sina/callback
	"requireState": true // 是否打开CSRF防御
}
```

### 自动封禁
```javascript
"autoban": { // 自动封号扩展
	"block": 3, // 被拦截超过一定数字自动封号
}
```

### 全局审核
```javascript
"audit": { // 审核扩展
}
```

### 直播同步

此扩展基于[danmu](https://github.com/littlecodersh/danmu)项目开发，需要安装Python 2.7+ 或 Python 3.5+。在启用前，你首先需要
```bash
pip install danmu
```

才可打开。

```javascript
"livesync": { // 新浪微博扩展
	"房间名": {
		"liveUrl": '', // 直播网站地址
	}
}
```
## 常见问题
### 数据库相关
``{ [Error: Connection lost: The server closed the connection.] fatal: true, code: 'PROTOCOL_CONNECTION_LOST' }``

请把MySQL的``wait_timeout``设置得大一些。

## 搭配项目

- [danmu-client](https://github.com/zsxsoft/danmu-client)

## 流程图

![流程图](http://zsxsoft.github.io/danmu-server/route.png)

## 协议
The MIT License (MIT)

## 博文
[弹幕服务器及搭配之透明弹幕客户端研究结题报告](http://blog.zsxsoft.com/post/15)

[弹幕服务器及搭配之透明弹幕客户端研究中期报告](http://blog.zsxsoft.com/post/14)

[弹幕服务器及搭配之透明弹幕客户端研究开题报告](http://blog.zsxsoft.com/post/13)

## 开发者
zsx - https://www.zsxsoft.com / 博客 - https://blog.zsxsoft.com
