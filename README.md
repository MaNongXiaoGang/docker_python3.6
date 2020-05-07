**今后会发布反爬虫教程，点波关注不迷路哦。如果文章有帮助，点颗星鼓励一下。**

## 一、安装docker
如果没有安装docker的可以参考：[Windows 10 下安装使用Docker](https://www.jianshu.com/p/c7d35f406598)

---
## 二、下载配置文件
[https://github.com/MaNongXiaoGang/docker_python3.6](https://github.com/MaNongXiaoGang/docker_python3.6)

---
## 三、配置文件修改

#### 1.配置文件说明

![docker配置文件结构.png](https://upload-images.jianshu.io/upload_images/14530364-20841e3a266dac29.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2.第一个Dockerfile文件
第一个Dockerfile文件，修改下载源，解决 apt-get 和 pip 下载超时的问题
```Dockerfile 
FROM python:3.6

MAINTAINER MaNongXiaoGang
ENV CODE /usr/src/code
RUN mkdir -p $CODE
# 设置工作目录
WORKDIR $CODE

# 下一级镜像执行
ONBUILD COPY ./requirements.txt $CODE
ONBUILD RUN pip install -U pip --default-timeout=10000 && \
            pip install --no-cache-dir -r requirements.txt --default-timeout=10000

# 更改pip源
ADD ./pip.conf $CODE
# 把当前目录文件添加到工作目录
ADD ./sources.list $CODE

# 修改apt-get源
RUN mv /etc/apt/sources.list /etc/apt/sources.list.bak && \
        mv sources.list /etc/apt/ && \
        sed -i "s@http://deb.debian.org@http://mirrors.aliyun.com@g" /etc/apt/sources.list && \
        mkdir /root/.pip/ && \
        mv pip.conf /root/.pip/pip.conf && \
        rm -rf /var/lib/apt/lists/* && \
        apt-get update && \
        apt-get install -y --no-install-recommends\
            # 下载安装 sudo，不然用 Pycharm 创建 Django 项目时会报错
            sudo \
            vim \
            # 安装ssh-server,远程连接
            openssh-server && \
        # 设置 root 用户密码
        echo 'root:qq123456' | chpasswd && \
        # 注意在 PermitRootLogin 前加 #，因为 sshd_config 文件里该参数是被注释的
        sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config  && \
        sed 's@sessions*requireds*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd && \
        echo "export VISIBLE=now" >> /etc/profile && \
        # 重启 SSH 激活配置
        service ssh restart

# 开放端口，多个以空格隔开
EXPOSE 22 5000
CMD ["/bin/bash"]


# FROM python:3.6 可以修改其它源镜像，我这里用python3.6做机器学习
# echo 'root:qq123456' | chpasswd  密码可修改，要记住密码，后面远程连接需要
# EXPOSE 22 5000 需要开放的端口
```
#### 2.第二个dockerfile文件
```Dockerfile
FROM python36:python3.6

MAINTAINER MaNongXiaoGang



```
#### 3.pip.conf文件
```
[global]
trusted-host=pypi.tuna.tsinghua.edu.cn
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host=pypi.tuna.tsinghua.edu.cn

```
#### 4.sources.list文件

```
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb http://mirrors.tuna.tsinghua.edu.cn/debian/ stretch main contrib non-free
# deb-src http://mirrors.tuna.tsinghua.edu.cn/debian/ stretch main contrib non-free
deb http://mirrors.tuna.tsinghua.edu.cn/debian/ stretch-updates main contrib non-free
# deb-src http://mirrors.tuna.tsinghua.edu.cn/debian/ stretch-updates main contrib non-free
deb http://mirrors.tuna.tsinghua.edu.cn/debian/ stretch-backports main contrib non-free
# deb-src http://mirrors.tuna.tsinghua.edu.cn/debian/ stretch-backports main contrib non-free
deb http://mirrors.tuna.tsinghua.edu.cn/debian-security stretch/updates main contrib non-free
# deb-src http://mirrors.tuna.tsinghua.edu.cn/debian-security stretch/updates main contrib non-free

# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb http://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free
# deb-src http://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free
deb http://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free
# deb-src http://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free
deb http://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free
# deb-src http://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free
deb http://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free
# deb-src http://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free

```

---

## 四、构造属于自己的镜像并且启动容器

#### 1. 跳转到第一个Dockerfile路径

![cd 到第一个Dockerfile文件.png](https://upload-images.jianshu.io/upload_images/14530364-2709dce0f28af50a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2. 根据python:3.6原镜像构造自己的```"python36:python3.6"```镜像
> docker build -t "python36:python3.6" -f Dockerfile .

#### 3. 跳转到MachineLearning路径
>  cd MachineLearning/

![cd 到MachineLearning路径，执行第二个Dockerfile.png](https://upload-images.jianshu.io/upload_images/14530364-9e21c10282d92d1c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 4. 根据上面构造的```"python36:python3.6"```构造```"machinelearning/python3.6"```镜像
>  docker build -t "machinelearning/python3.6" -f Dockerfile .

#### 5. 创建启动```"machinelearning/python3.6"```容器
> docker run -it -v /home/lfg/Desktop/code/docker/python36/MachineLearning/data:/usr/src/code/data -p 10001:22 machinelearning/python3.6

启动成功截图：
![自定义docker容器启动成功.png](https://upload-images.jianshu.io/upload_images/14530364-cb1e79d5ec9d27cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**注意：
 -v 参数是挂载数据卷，这里需要修改成你主机的代码路径，记住你的修改，我们下面```pycharm```连接需要使用。  
格式: -v 主机路径: 容器路径**

---
## 五、PyCharm连接我们创建好的容器中的python环境
#### 1、进入容器终端，查询python执行路径。

![查询python执行路径.png](https://upload-images.jianshu.io/upload_images/14530364-d8a33c7195c55cb3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2、这一步是检查，直接输入路径文件，查看是不是python3.6版本路径
![这一步是检查，直接输入路径文件，查看是不是python3.6版本路径.png](https://upload-images.jianshu.io/upload_images/14530364-03bf09c8625bcfe8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 3、点击设置
![添加docker环境变量一 点击设置.png](https://upload-images.jianshu.io/upload_images/14530364-97a6bf2d16d7a66e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 4、点击添加环境
![添加docker环境变量二 点击添加环境.png](https://upload-images.jianshu.io/upload_images/14530364-175f906fac1ceba6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 5、 添加配置信息
![添加docker环境变量三 添加配置信息.png](https://upload-images.jianshu.io/upload_images/14530364-9f50bb55807fa359.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 6、 添加配置信息-密码
![添加docker环境变量三 添加配置信息-密码.png](https://upload-images.jianshu.io/upload_images/14530364-141588e1243f1360.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 7、 配置执行路径和同步路径
![添加docker环境变量四 配置执行路径同步路径.png](https://upload-images.jianshu.io/upload_images/14530364-619bb2ffc78a3a0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**注意：
这里需要主要同步路径，Local Path 填写你主机代码路径，或者你准备在某个路径写代码，就填哪个路径。 Remote Path 填写数据卷路径，上面有设置的**

---
#### 成功截图
![添加成功截图.png](https://upload-images.jianshu.io/upload_images/14530364-7c6924d5e5c1b3c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




