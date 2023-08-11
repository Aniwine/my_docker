#编译说明
0.下载docker文件，其目录结构为：
      |-Dockerfile
      |-Miniconda3-py310_23.5.2-0-Linux-x86_64.sh
      |-pycocotools-2.0.6.tar.gz
      |-README.txt
1.查看电脑ai用户uid和gid：id ai
2.将查到的uid、gid替换Dockerfile第31行处的uid、gid并保存

3.构建镜像,其中.表示Dockerfile所在目录，包含了所需拷贝至docker的文件
     docker build -t edu_photo:cuda_py3.10 .

4.创建容器
#源代码通过文件夹映射方式传递到docker容器内，源代码目录结构为：
#注：.../data/test下包含的为项目所有文件(...代表完整路径)，目录结构为
     .
    └── data
    └── test
        ├── application
        └── serve
#则将.../data/test文件夹映射到容器内/project文件夹下，创建命令如下：
     docker run -itd --gpus all --shm-size=8g -u ai --name lc_photodevelop -p 12080:8080 -p 12081:8081 -p 12082:8082 -p 12083:8083 -p 
     12084:8084  -p 12085:8085 -p 12086:8086 -p 13022:22  -v /sda2/data/development/project/luocheng/data/test:/project/ 
     -v  /usr/local/cuda-11.2/targets/x86_64-linux/lib:/usr/local/cuda-11.2/lib64 edu_photo:cuda_py3.10 /bin/bash

5.使用说明
     1).请使用 source activate base激活虚拟环境，使用base环境运行代码
     2).远程连接请先开启ssh。

6.注意事项
     1).pytorch的版本需要兼容宿主机的cuda版本，默认为10.2
     2).宿主机的docker版本需大于19.