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
#注：/sda2/data/development/project/luocheng/data/test下包含的为项目所有文件，目录结构为（省略了前面部分）
     .
    └── data
    └── test
        ├── application
        └── serve
#则将.../data/test文件夹映射到容器内/project文件夹下，创建命令如下：
     docker run -itd --gpus all --shm-size=32g  --name yolos -p 13080:8080 -p 13081:8081 -p 13082:8082 -p 13083:8083 -p 
     13084:8084  -p 13085:8085 -p 13086:8086 -p 14022:22  -v /home/luocheng/docker_env/volume:/project/ 
     image_process:v1 /bin/bash

5.使用说明
     1).请使用 source activate base激活虚拟环境，使用base环境运行代码
     2).远程连接请先开启ssh。

6.注意事项
     1).pytorch的版本需要兼容宿主机的cuda版本，默认为10.2，如需更换版本，
        请到https://pytorch.org/get-started/previous-versions/    查询并修改Dockerfile第105行处；
     2).宿主机的docker版本需大于19.
