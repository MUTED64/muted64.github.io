---
title: Ubuntu+gunicorn+nginx在ECS上搭建Flask环境
date: 2020-10-01 13:51:00
tags:
	- Python
	- Web
categories:
	- 开发
---

网络上能搜索到的资源基本都有些疏漏的地方，把自己的配置过程Po上来做个参考防止重复踩坑。

<!-- more -->

https://zhuanlan.zhihu.com/p/22126999 这篇专栏写得还不错，参考了其中的一些内容做了自己的配置，不过这篇里也有一些没写清楚的超级坑点，坑了我好久，我在这里补充一些内容来防止重复踩坑了。

相关版本清单如下：

> 系统： Ubuntu 16.04 64位
>
> WSGI： Gunicorn
>
> 进程监控： Supervisor
>
> 服务器软件：nginx
>
> Python版本： python3.5.2

# 云服务器相关

笔者用的是阿里云ECS的学生机，当然你用其它家的产品也大同小异啦。

ssh连接：

```shell
ssh root@xxx.xxx.xxx.xxx
```

建立普通用户账号：

```shell
useradd www -d /home/www -m
passwd www
```

# 安装相关支持

```shell
pip install flask
apt install python-pip
pip install virtualenv
```

如果安装virtualenv遇到以下报错：

```
IOError: [Errno 2] No such file or directory: '/tmp/pip-kYFqUa-build/setup.py'
```

尝试：

```shell
pip install --upgrade pip
```

# 页面配置

先保留root终端，新开一个终端并使用刚才新建的用户登录服务器。

```
ssh www@xxx.xxx.xxx.xxx
```

创建网站根目录：

```
cd ~
mkdir blog
```

创建了工程的文件夹，这里用blog来代替，根据实际情况要换成其它的命名。

下面创建虚拟环境：

```
cd blog
virtualenv -p /usr/bin/python3 venv
```

使用SFTP可以上传本地的工程文件到blog文件夹。建议使用www用户。

pip包生成requiements.txt:

```
pip freeze > requirements.txt
```

# Gunicorn配置

下面配置Gunicorn：

```
source venv/bin/activate
pip install gunicorn
cd /home/www/blog
vim gunicorn.conf
```

之后用vim写入：

```
# 进程数
workers = 3
# 监听本地的哪个端口
bind = '127.0.0.1:8000'
```

`:wq`保存退出。

安装组件：

```
pip install -r requirements.txt
```

创建日志文件夹：

```
mkdir logs
```

退出虚拟环境：

```
deactivate
```

# Supervisor配置

**使用root用户：**

配置supervisor：

```
apt install supervisor
cd /etc/supervisor/conf.d
vim blog.conf
```

vim写入：

```
# 进程的名字，取一个以后自己一眼知道是什么的名字。
[program:blog]
# 定义命令。你只要注意后面的目录对就行。特别注意「run:app」的run，这个名字是你网站应用的文件名。如果是run.py，就写run。
command=/home/www/blog/venv/bin/gunicorn run:app -c /home/www/blog/gunicorn.conf
# 网站目录
directory=/home/www/blog
# 进程所属用户。
user=www
# 自动重启设置。
autostart=true
autorestart=true
# 日志存放位置。
stdout_logfile=/home/www/blog/logs/gunicorn_supervisor.log
# 设置环境变量。这里这行的意思是：设置环境变量MODE的值为UAT。请根据自己的需要配置，如没有需要这行可以删除。
environment = MODE="UAT"
```

`:wq`保存。

**回到supervisor的目录：**

```
cd /etc/supervisor
```

启动supervisor：

```
service supervisor start
```

加载配置：

```
sudo supervisorctl reread
supervisorctl update
sudo supervisorctl start blog
```

# Nginx配置

开始配置nginx：

```
apt install nginx
cd /etc/nginx/sites-avaliable
vim blog
```

输入对于blog的配置：

```
server {  
        listen   80;  
        server_name www.xxx.com xxx.com;

        root /home/www/blog;
        access_log /home/www/blog/logs/access.log;
        error_log /home/www/blog/logs/access.log;

        location / {  
            proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;  
            proxy_set_header Host $http_host;  
            proxy_redirect off;  
            if (!-f $request_filename) {  
                proxy_pass http://127.0.0.1:8000;  
                break;  
            }  
        }  
    }
```

**检查并根据实际情况修改上面的server_name和root配置。**检查监听端口和上面设置是否一致。`:wq`保存。

下面链接配置文件到sites-enabled:

```
cd /etc/nginx/sites-enabled/
ln -s /etc/nginx/sites-avaliable/blog ./blog
```

重启nginx：

```
service nginx restart
```

阿里云安全组配置允许入方向80端口。完成。

键入域名或ip即可访问。