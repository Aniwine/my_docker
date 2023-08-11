直接安装python3.10会导致冲突问题，因为Ubuntu20.04自带了python3.8，安装mmyolo时导入找不到已安装的torch模块,
可以通过在mmyolo的安装文件里使用sys.path.append(path/to/your/python3.10/site-packages)解决

#构建镜像过程中会产生Exited退出的容器以及none的镜像，参考
https://blog.csdn.net/qq_24596243/article/details/89840490进行删除