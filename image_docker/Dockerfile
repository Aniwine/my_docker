FROM nvidia/cuda:11.2.2-cudnn8-runtime-ubuntu20.04
LABEL maintainer="luocheng@chinaedu.net"

#设置环境变量
ENV LANG C.UTF-8
ENV PATH $PATH:/usr/local/bin

#更改apt源
RUN echo "\
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse\n\
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse\n\
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse\n\
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse\n\
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse\n\
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse\n\
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse\n\
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse\n\
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse\n\
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse"\
>> /etc/apt/sources.list

# 创建用户组
RUN groupadd -g 1002 ai

#创建普通用户，并分配sudo权限
ARG user=lc
ARG passwd=123
ARG userhome=/home/${user}

#指定和宿主机上ai用户同样的uid（名字可不同）
RUN adduser --disabled-password --gecos "" -uid 1002 -gid 1002 ${user} && \
    usermod -aG sudo ${user} && \
    mkdir -p /etc/sudoers.d/ && \
    echo "%sudo  ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/nopasswd && \
    echo root:${passwd} | chpasswd && \
    echo ${user}:${passwd} | chpasswd && \
    touch ${userhome}/.sudo_as_admin_successful

#添加公钥
RUN apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/3bf863cc.pub

#安装基础环境,并开启ssh
RUN 	chmod 777 /tmp \
	&& apt-get clean \
	&& apt-get update \
	&& DEBIAN_FRONTEND=noninteractive apt-get install -y tzdata \
	&& apt-get install openssl libssl-dev -y \
	&& apt-get install -y gcc cmake make cron rsyslog apt-utils build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev libffi-dev liblzma-dev \
    	&& apt-get install -y vim zip unzip psmisc openssh-server \
    	&& apt-get clean \
    	&& apt-get autoclean \
	&& /etc/init.d/ssh start 

# 开启所有用户的ssh登录许可
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config && \
echo 'sshd:ALL' >> /etc/hosts.aldlow 

#安装包含python3.10的miniconda
COPY	Miniconda3-py310_23.5.2-0-Linux-x86_64.sh /tmp/ 
RUN	chmod 777 /tmp/Miniconda3-py310_23.5.2-0-Linux-x86_64.sh \
	&& /bin/bash /tmp/Miniconda3-py310_23.5.2-0-Linux-x86_64.sh -b -p /usr/local/miniconda3  
RUN	echo "export PATH=/usr/local/miniconda3/bin:$PATH" >> ~/.bashrc \
	&& /bin/bash -c "source ~/.bashrc" \
	&& rm -f /tmp/Miniconda3-py310_23.5.2-0-Linux-x86_64.sh

#构建软连接
RUN	ln -s -f /usr/local/miniconda3/bin/pip /usr/bin/pip \
	&& ln -s -f /usr/local/miniconda3/bin/pip /usr/bin/pip3

#更改pip源
RUN mkdir ~/.pip && cd ~/.pip && \
echo "\
[global]\n \
index-url = https://pypi.tuna.tsinghua.edu.cn/simple/\n \
\n \
[install]\n \ 
https://pypi.tuna.tsinghua.edu.cn\n" \
> ~/.pip/pip.conf

# 更改Conda国内源
RUN echo "\
channels:\n\
   - defaults\n\
show_channel_urls: true\n\
channel_alias: https://mirrors.tuna.tsinghua.edu.cn/anaconda\n\
default_channels:\n\
   - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main\n\
   - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free\n\
   - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r\n\
   - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro\n\
   - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2\n\
custom_channels:\n\
   conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud\n\
   msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud\n\
   bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud\n\
   menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud\n\
   pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud\n\
   simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud\n"\
> ~/.condarc

# 配置普通用户环境变量
RUN echo export PATH=$PATH:/usr/local/miniconda3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin >> ${userhome}/.bashrc &&  /bin/bash -c 'source ${userhome}/.bashrc'

#安装pytorch，由于没有11.2，选择向下兼容的10.2
RUN	pip install torch==1.12.0+cu102 torchvision==0.13.0+cu102 torchaudio==0.12.0 --extra-index-url https://download.pytorch.org/whl/cu102

#安装mmcv，mmengine，mmdet
WORKDIR /my_lib
COPY	pycocotools.tar.gz /my_lib/ 

RUN	cd /my_lib  \
	&& chmod 777 /my_lib/pycocotools.tar.gz \
	&& tar -xzf pycocotools.tar.gz \
	&& cd pycocotools-2.0.6 \
	&& pip install -e . \
	&& rm -f /my_lib/pycocotools.tar.gz

WORKDIR /project
CMD	/etc/init.d/ssh start

#项目代码
#通过文件映射的方式传递到docker容器内。


