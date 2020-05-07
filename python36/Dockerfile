FROM python:3.6

MAINTAINER MaNongXiaoGang
ENV CODE /usr/src/code
RUN mkdir -p $CODE
# 设置工作目录
WORKDIR $CODE

# 下一级镜像执行
ONBUILD COPY ./requirements.txt $CODE
ONBUILD RUN pip install -U pip --default-timeout=10000 -i https://pypi.tuna.tsinghua.edu.cn/simple && \
            pip install --no-cache-dir -r requirements.txt --default-timeout=10000 -i https://pypi.tuna.tsinghua.edu.cn/simple

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

# 端口映射，多个以空格隔开
#EXPOSE 22 5000
EXPOSE 22:10122
CMD ["/bin/bash"]





