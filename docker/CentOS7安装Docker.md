## 安装Docker

### 安装依赖
* CentOS 7
* Docker 1.12 (Docker要求64位的系统且内核版本至少为3.10)
* Docker Compose

### 安装Docker
安装Docker在不同平台、不同操作系统中方式不尽相同， 这里还是推荐使用[官方CentOS](https://docs.docker.com/engine/installation/linux/centos/)的安装方式, 其他方式请自行搜索， 另外， 特别推荐使用阿里云提供的`Docker Hub`镜像站点， 为你提供专属的Docker加速服务。

> [阿里云ECS安装Docker](https://help.aliyun.com/document_detail/51853.html) 

```
$ sudo yum install -y yum-utils

$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

$ sudo yum makecache fast

$ sudo yum -y install docker-ce

# Add docker group
$ sudo groupadd docker

# Add user to docker group
$ sudo usermod -aG docker $USER

## start up docker
$ sudo systemctl enable docker

$ sudo systemctl start docker
```
### 阿里云Docker Hub镜像站点加速
[阿里云Docker Hub加速器](https://cr.console.aliyun.com/#/accelerator), 需要开通阿里云账号，每一个账户拥有专属加速地址。

```
$ sudo mkdir -p /etc/docker
$ sudo tee /etc/docker/daemon.json <<-'EOF'
{
	"registry-mirrors": ["https://sxykhwc3.mirror.aliyuncs.com"]
}
EOF
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

### 安装Docker Compose
推荐Docker Compose官方[Github仓库](https://github.com/docker/compose/releases)安装方式， 请先选择一个版本。

```
$ curl -L https://github.com/docker/compose/releases/download/1.13.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
$ chmod +x /usr/local/bin/docker-compose
```

### 使用ctop查询容器占用资源
[ctop](https://github.com/bcicen/ctop)可以用于查询容器资源占用情况，推荐安装， 比如我们的服务器安装了Gitlab与LNMP docker后的使用情况

```
$ ctop
 ctop - 15:36:35 CST      10 containers

   NAME                        CID                         CPU                         MEM                         NET RX/TX                   IO R/W                      PIDS

 ◉  gitlabdocker_gitlab_1       97d5ba4b4918                             5%                     1.99G / 7.64G       948M / 1.6G                 120M / 776K                 0
 ◉  gitlabdocker_postgresql_1   146b662e4d62                             0%                      75M / 7.64G        897K / 8M                   24M / 0B                    0
 ◉  gitlabdocker_redis_1        3bcf1582f892                             2%                      14M / 7.64G        1.6G / 940M                 5M / 0B                     0
 ◉  lnmp-call-websocket         eff86b31f2ba                             0%                      66M / 7.64G        3K / 648B                   20M / 0B                    0
 ◉  lnmp-mariadb                bd3cecff945e                             0%                     179M / 7.64G        90K / 276K                  27M / 0B                    0
 ◉  lnmp-nginx                  f4452c868dcc                             0%                      8M / 7.64G         14M / 5M                    5M / 0B                     0
 ◉  lnmp-php-fpm                a68c55c28995                             0%                      72M / 7.64G        1M / 13M                    20M / 0B                    0
 ◉  lnmp-php-supervisor         15182399966b                             1%                     1.8G / 7.64G        92M / 145M                  26M / 0B                    0
 ◉  lnmp-redis                  279b2f995b2a                             0%                      8M / 7.64G         62M / 16M                   2M / 0B                     0
 ◉  lnmp-www                    09c684094c18                              -                           -             -                           -                           -
```

## 参考
> [Docker -- 从入门到实践](https://yeasy.gitbooks.io/docker_practice/content/install/centos.html)
