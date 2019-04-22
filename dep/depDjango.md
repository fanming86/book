# 部署django

---

## 说明
使用 uwsgi、nginx 来部署django项目
操作系统使用的centos7.5
python版本 3.6
    python使用虚拟环境
    django2.2


## 1、安装项目所需要的环境
### 1.1、安装python3.6
#### 使用源码安装python36
>由于本地开发使用的python3.6.6版本，直接下载python3.6.6的源码包

1）下载python源码包,并解压
```bash
wget https://www.python.org/ftp/python/3.6.6/Python-3.6.6.tgz
tar -xzvf Python-3.6.6.tgz
```
2）安装编译所需的包
```bash
yum install gcc -y
```
3) 编译并安装
```bash
#进入解压后的目录
cd Python-3.6.6
#执行安装配置，指定安装路径
./configure --prefix=/usr/local/python3
#build和安装
make && make install
```
在执行最后一步时，报错`zipimport.ZipImportError: can't decompress data; zlib not available`
安装此包可以解决：`yum install zlib-devel -y `

4）安装完成后，在 `/usr/local/python3/bin/` 目录可以看到安装的python 和pip
将他们连接到 /usr/bin/ 目录下，使其成为全局命令
```bash
[root@localhost bin]# ln -s /usr/local/python3/bin/python3 /usr/bin/python3
[root@localhost bin]# 
[root@localhost bin]# ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
```
为了和python2 区分开，所以使用python3 ，这里最好不要将默认的python 连接到python3

### 1.2、安装虚拟环境
```bash
[root@localhost ~]# pip3 install virtualenv
[root@localhost ~]# mkdir /djangoEnv
[root@localhost ~]# cd /djangoEnv/
[root@localhost djangoEnv]# virtualenv myenv
[root@localhost djangoEnv]# cd myenv/
[root@localhost myenv]# source bin/activate
(myenv) [root@localhost myenv]# pip install django==2.2
```

### 1.3、将项目上传至服务器


### 1.4、安装django及项目所需模块

```bash
#根据开发环境导出的文件安装
(myenv) [root@localhost mysite]# pip install -r requments.txt
```

## 2、安装和配置uwsgi 

```bash
(myenv) [root@localhost mysite]# pip install uwsgi
```

#### uwsgi配置文件
1）在项目根目录创建目录 `script` ,并在此目录下创建文件 `uwsgi.ini` 文件，用于启动uwsgi
uwsgi.ini 文件内容如下
```bash
# uwsig使用配置文件启动
[uwsgi]
# 项目目录
chdir = /var/www/mysite/
# 指定项目的application
module=mysite.wsgi:application
# 指定sock的文件路径       
socket = /var/www/mysite/script/uwsgi.sock
# 进程个数       
workers=5
pidfile = /var/www/mysite/script/uwsgi.pid
# 指定静态文件
static-map = /static=/var/www/mysite/static
# 启动uwsgi的用户名和用户组
uid=root
gid=root
# 启用主进程
master=true
# 自动移除unix Socket和pid文件当服务停止的时候
vacuum=true
# 序列化接受的内容，如果可能的话
thunder-lock=true
# 启用线程
enable-threads=true
# 设置自中断时间
harakiri=30
# 设置缓冲
post-buffering=4096
# 设置日志目录
daemonize = /var/www/mysite/script/uwsgi.log
# 设置虚拟环境目录
home = /djangoEnv/myenv/

```

2）配置完成后，使用以下命令来启动uwsgi

```bash
(myenv) [root@localhost script]# uwsgi --ini uwsgi.ini      #启动uwsgi
(myenv) [root@localhost script]# uwsgi --reload uwsgi.pid   #重启
(myenv) [root@localhost script]# uwsgi --stop  uwsgi.pid    #停止
```



## 3、安装和配置nginx
### 3.1、安装nginx
#### 使用yum 安装nginx
1）、因为nginx这个服务不是yum库中自带的，所以需要先在系统中添加nginx的源
```bash
rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
```
2）、然后使用`yum` 安装nginx
```bash
yum install nginx -y
```

### 3.2、配置nginx

在nginx 配置文件子目录 conf.d 中创建文件`django.conf`
内容如下：
```bash
server {
#django

    listen       80 default_server;
        server_name  192.168.15.136;
        location / {
                include        uwsgi_params;
                uwsgi_pass     unix:/var/www/mysite/script/uwsgi.sock;
        }
        
        location /static/ {
                alias /var/www/mysite/static/;
                index index.html index.htm;
        }

}
```





