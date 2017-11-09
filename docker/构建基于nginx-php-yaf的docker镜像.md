## 基于nginx-php-yaf构建docker镜像
> 需求 : php7.1 + nginx     
> php扩展： yaf、redis、ldap、pdo、mbstring、 mcrypt

阅读完本文后，你能解决以下常见问题：

* 如何写Dockerfile，并通过Dockerfile构建镜像。
* 如何通过supervisord管理进程，并将进程日志通过docker logs {container}输出

## 实现步骤
我们的镜像基于`centos:7`系统， Dockerfile代码如下：

```
FROM centos:7
MAINTAINER zhaowei@outlook.com
RUN rpm -Uvh http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm \
	&& rpm -Uvh https://sp.repo.webtatic.com/yum/el7/webtatic-release.rpm \
	&& yum update -y \
	&& yum install -y wget \
		sed \ 
		gcc \
		gcc-c++ \
		gd \
		gd-devel \
		gmp-devel \
		epel-release \
		net-tools \
		ntpdate \
		ntp \
		openssh-clients \ 
		curl \
		crontabs \
		openssl \
		openssl-devel \
		nginx \
		squid \
		php71w \
		php71w-fpm \
		php71w-gd \
		php71w-soap \
		php71w-pdo_mysql \
		php71w-pear \
		php71w-devel \	
		php71w-mbstring \
		php71w-mcrypt \
		php71w-ldap \
	&& yum update -y ntp ntpdate kernel-headers \
	&& yum clean all \
	&& sed -i 's/http_access deny all/#http_access deny all/g' /etc/squid/squid.conf \
	&& cd /tmp \
	&& wget -O get-pip.py https://bootstrap.pypa.io/get-pip.py \
	&& python get-pip.py \
	&& pip install supervisor \
	&& mkdir -p /etc/supervisor.d/*.conf \
	&& pecl install mongodb \
	&& pecl install yaf \
	&& wget -c https://github.com/phpredis/phpredis/archive/3.1.4.tar.gz \
        && tar zxvf 3.1.4.tar.gz \
        && cd phpredis-3.1.4 \
        && phpize \
        && ./configure \
        && make \
        && make install \
        && cd .. \
        && rm -rf phpredis* \
	&& rm 3.1.4.tar.gz \ 
	&& echo "" >> /etc/php.ini \
	&& echo "extension=redis.so" >> /etc/php.ini \
	&& echo "extension=yaf.so" >> /etc/php.ini \
	&& echo "extension=mongodb.so" >> /etc/php.ini \
	&& echo "[yaf]" >> /etc/php.ini \
 	&& echo "yaf.use_namespace = 1" >> /etc/php.ini 
COPY ./supervisord.conf /etc/
COPY ./default.conf /etc/nginx/conf.d/
COPY ./nginx.conf /etc/supervisor.d/
COPY ./php.conf /etc/supervisor.d/
COPY ./ntpdate.conf /etc/supervisor.d/
COPY ./nginx.conf.def /etc/nginx/nginx.conf
COPY ./index.php /data/work/code/
EXPOSE 80 443 3128
WORKDIR /data/work/code
CMD ["supervisord","-c","/etc/supervisord.conf"]
```

##  问题
#### 子进程stdout如何重定向到supervisord
> `Supervisor`的监督进程`supervisord`可以捕获所管理子进程的stdout及stderr，并可以配置为写入日志文件   

镜像初步构建成功时，运行容器，并通过 `docker logs -f {container}` 查看容器日志，总是提示错误， 如下：

```
2017-11-09 07:41:41,256 CRIT uncaptured python exception, closing channel <POutputDispatcher at 36463248 for <Subprocess at 35644984 with name nginx in state RUNNING> (stderr)> (<type 'exceptions.IOError'>:[Errno 29] Illegal seek [/usr/lib/python2.7/site-packages/supervisor/supervisord.py|runforever|227] [/usr/lib/python2.7/site-packages/supervisor/dispatchers.py|handle_read_event|232] [/usr/lib/python2.7/site-packages/supervisor/dispatchers.py|record_output|166] [/usr/lib/python2.7/site-packages/supervisor/dispatchers.py|_log|142] [/usr/lib/python2.7/site-packages/supervisor/loggers.py|info|275] [/usr/lib/python2.7/site-packages/supervisor/loggers.py|log|293] [/usr/lib/python2.7/site-packages/supervisor/loggers.py|emit|186] [/usr/lib/python2.7/site-packages/supervisor/loggers.py|doRollover|211])
```
经过了N多次查询，N多次尝试，终于找到原因，原来是在配置`supervisord`管理的子进程文件时，如果配置了`stdout_logfile=/dev/stdout`, 则必须一块配置`stdout_logfile_maxbytes=0`

查找的资料说明如下： 

If anyone stumbles upon this error like me, be aware that the mentioned options apply to the [supervisord] section, within [program:x] sections, you need to use `stdout_logfile_maxbytes = 0 `and/or `stderr_logfile_maxbytes = 0` respectively.     

using `stdout_logfile=/dev/stdout` along with `stdout_logfile_maxbytes=0`


<font color="red">对于需要将supervisord管理的子进程的日志输出到stdout的program， 配置如下：</font>

```
[program:php7-fpm]
command=/usr/local/sbin/php-fpm
autostart = true
stderr_logfile = /dev/stdout
stdout_logfile = /dev/stdout
stdout_logfile_maxbytes = 0
stderr_logfile_maxbytes = 0
```

> 参见： http://veithen.github.io/2015/01/08/supervisord-redirecting-stdout.html

#### php-fpm 的错误日志如何定向到docker logs 
看到 https://github.com/docker-library/php/issues/63 解释，将`error_log`及`access.log`的配置改成如下`error_log=/proc/self/fd/2` 和 `access.log=/proc/self/fd2`即可，一直不明白，`/proc/self/fd/2`和`/dev/stdout` `/dev/stderr`有什么联系，经过`ls -al /dev` 才明白： 

```
lrwxrwxrwx  1 root root   15 Nov  9 07:44 stderr -> /proc/self/fd/2
lrwxrwxrwx  1 root root   15 Nov  9 07:44 stdin -> /proc/self/fd/0
lrwxrwxrwx  1 root root   15 Nov  9 07:44 stdout -> /proc/self/fd/1
```

对于我们的需求，是将错误及access信息都在docker logs 中显示，所以，我们配置`php-fpm.conf`如下： 

```
[global]
error_log=/proc/self/fd/1
```

文件`/etc/php-fpm.d/www.conf`配置如下：

```
php_admin_value[error_log] = /proc/self/fd/1
slowlog = /proc/self/fd/1
catch_workers_output = yes
clear_env=no
```




