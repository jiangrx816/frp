# frp实现内网穿透

### 仓库目录结构说明
- ./frp/frpc.ini  frpc.ini是客户端的配置文件
- ./frpc.yml      docker-compose脚本启动客户端
- ./frp/frps.ini  frps.ini是服务端的配置文件
- ./frps.yml      docker-compsoe脚本启动服务端
- ./nginx/nginx.conf nginx配置文件
- ./nginx.yml     docker-compose脚本启动nginx
- ./windows       该目录下是windows系统下的软件与配置

## 部署环境准备

### docker和docker-compose安装 准备操作
安装相关工具类

```bash
yum install -y yum-utils device-mapper-persistent-data lvm2
```

配置docker仓库

```bash
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

aliyun的源

```bash
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

```

安装docker

```bash
yum install docker-ce
```
启动docker

```bash
systemctl start docker
```

设置开机自启

```bash
chkconfig docker on
```

### **配置163镜像与存储目录**

```bash
vim /etc/docker/daemon.json
```

registry-mirrors 为镜像地址
docker 版本<20 graph 为存储目录 建议不要使用默认的 否则空间会不够用
docker 版本>20 graph已经弃用 需使用 data-root

```json
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"],
  "data-root": "/home/docker"
}
```

```bash
systemctl daemon-reload
systemctl restart docker
```


### docker-compose安装

#### 手动安装

下载地址：https://github.com/docker/compose/releases/
服务器无法访问GitHub，所以先下载对应的版本到本地，并改名为 docker-compose，然后上传到服务器的/usr/local/bin目录

####  授权
```bash
chmod +x /usr/local/bin/docker-compose
```

## 开始部署
### 【步骤一】部署nginx
复制nginx.conf到/docker/nginx/conf目录下（不要修改或修改为nginx.yml对应配置的挂载目录下）
复制nginx.yml到/docker/nginx目录下
```bash
cd /docker/nginx
$ docker-compose -f nginx.yml up -d nginx
```

### 【步骤二】部署frp-server
修改frps.ini配置适配自己的域名后，复制frps.ini和frps.yml到/home/frps目录下（或自定义目录）
```bash
$ cd /home/frps
$ docker-compose -f frps.yml up -d frps
```

### 【步骤三】linux部署frp-client
修改frpc.ini配置适配自己的域名后，复制frpc.ini和frpc.yml到/home/frpc目录下（或自定义目录）
```bash
$ cd /home/frpc
$ docker-compose -f frpc.yml up -d frpc
```

### 【步骤三】windows部署frp-client
复制windows到指定的目录下（可任意选择存放目录） ，打开frpc.ini进行配置 ，启动run.bat
