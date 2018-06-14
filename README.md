# AI 体验中心

在微软云容器服务上创建基于 Python 和 Redis 的微信小程序

## 开发环境
Ubuntu 16.04 (64位)

Python 3.5.2

## 安装依赖项

- [Flask](http://flask.pocoo.org/)
- [Redis](https://github.com/rgl/redis/downloads)

```bash
$ pip install -r requirements.txt
```

## 本地编配

使用 docker-compose 本地编配：

```bash
$ docker-compose up
```

开打浏览器，可见页面上显示了当前计数值。刷新网站，会看到这个值增加：

![image](https://github.com/foamliu/Wechat-Applet/raw/master/images/docker-compose.png)

## 构建镜像并推送到微软云镜像仓库

使用az acr create 命令创建Azure镜像仓库:

```bash
$ az acr create --resource-group azureimgbot --name WechatAppletReg --sku Basic
```


docker build -t registry.aliyuncs.com/acs-sample/flask .