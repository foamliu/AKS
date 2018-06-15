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

- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

## 本地编配

使用 docker-compose 本地编配:

```bash
$ docker-compose up
```

开打浏览器，可见页面上显示了当前计数值。刷新网站，会看到这个值增加:

![image](https://github.com/foamliu/Wechat-Applet/raw/master/images/docker-compose.png)

使用docker images命令可查看镜像列表:

```bash
$ docker images
```

![image](https://github.com/foamliu/Wechat-Applet/raw/master/images/docker_images.png)


## 配置微软云

1.安装 kubectl:

```bash
$ az aks install-cli
```

2.在微软云创建一个资源组：“myResourceGroup”

3.创建 AKS 集群:

```bash
$ az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 2 --generate-ssh-keys
```

4.将kubectl配置为连接到Kubernetes集群

使用az aks get-credentials命令，此步骤下载凭证并配置Kubernetes CLI以使用它们:

```bash
$ az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

5.验证与群集的连接

使用kubectl get命令返回群集节点的列表:

```bash
$ kubectl get nodes
```

![image](https://github.com/foamliu/Wechat-Applet/raw/master/images/azure.png)


## 推送镜像到微软云仓库

1.创建Azure镜像仓库:

```bash
$ az acr create --resource-group myResourceGroup --name foamliu --sku Basic
```

2.测试可以登录:

```bash
$ az acr login --name foamliu
```

3.获取登录服务器名:

```bash
$ az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

4.给本地容器打标:

```bash
$ docker tag wechatapplet_web foamliu.azurecr.io/wechatapplet_web:v1
```

5.查看镜像，确认标签已经打上去:

```bash
$ docker images
```

6.推送镜像到微软云仓库:

```bash
$ docker push foamliu.azurecr.io/wechatapplet_web
```

![image](https://github.com/foamliu/Wechat-Applet/raw/master/images/docker_push.png)

## 运行应用

1.使用 kubectl apply 命令运行应用:

```bash
$ kubectl apply -f wechat-applet.yaml
```

![image](https://github.com/foamliu/Wechat-Applet/raw/master/images/kubectl_apply.png)

在应用运行时，将创建一个Kubernetes服务，将应用前端开放。要监视进度，使用带有--watch参数的 kubectl get service命令:

```bash
$ kubectl get service wechat-applet-front --watch
```

一旦EXTERNAL-IP地址从Pending状态变为IP地址，即可按CTRL-C停止kubectl监视进程。

![image](https://github.com/foamliu/Wechat-Applet/raw/master/images/kubectl_get_service.png)



构建镜像并推送到微软云镜像仓库

使用az acr create 命令创建Azure镜像仓库:

```bash
$ az acr create --resource-group azureimgbot --name WechatAppletReg --sku Basic
```


docker build -t registry.aliyuncs.com/acs-sample/flask .