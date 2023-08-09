直接安装python3.10会导致冲突问题，因为Ubuntu20.04自带了python3.8，安装mmyolo时导入找不到已安装的torch模块,
可以通过在mmyolo的安装文件里使用sys.path.append(path/to/your/python3.10/site-packages)解决