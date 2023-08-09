#编译说明

#构建镜像,其中.表示Dockerfile所在目录，包含了所需拷贝至docker的文件
#docker build -t edu_photo:cuda_py3.10 .
#创建容器
#docker run -itd --gpus all --shm-size=8g -u ai --name lc_photodevelop -p 12080:8080 -p 12081:8081 -p 12082:8082 -p 12083:8083 -p 
12084:8084  -p 12085:8085 -p 12086:8086 -p 13022:22  -v /sda2/data/development/project/luocheng/data/test:/project/ 
-v  /usr/local/cuda-11.2/targets/x86_64-linux/lib:/usr/local/cuda-11.2/lib64 edu_photo:cuda_py3.10 /bin/bash
#注：.../data/test下包含的为应用文件，目录结构为
├── application
├── cuda.python3.10
├── docker
└── serve
application为照片检测应用，serve为web服务
此目录将映射到docker的/project


#使用说明
1.请使用 source activate base激活虚拟环境，使用base环境运行代码
2.远程连接请先开启ssh。

#注意事项
1.Dockerfile里创建普通用户ai是指定的uid和gid是宿主上同样名称ai用户的uid和gid，按需更改；pytorch的版本需要兼容宿主机的cuda版本
2.宿主机的docker版本需大于19.