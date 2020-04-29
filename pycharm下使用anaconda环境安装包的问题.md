## pycharm下使用anaconda环境安装包的问题

pip install 包名提示:
```pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.
```
原因是因为openssl版本过低或者不存在,解决办法：
1. linux环境
(1)查看openssl安装包，发现缺少openssl-devel包
 
[root@localhost ~]# rpm -aq|grep openssl
 
openssl-0.9.8e-20.el5
 
openssl-0.9.8e-20.el5
(2)yum安装openssl-devel 

[root@localhost ~]# yum install openssl-devel -y 

查看安装结果 

[root@localhost ~]# rpm -aq|grep openssl 

  openssl-0.9.8e-26.el5_9.1
 
openssl-0.9.8e-26.el5_9.1
 
openssl-devel-0.9.8e-26.el5_9.1
 
openssl-devel-0.9.8e-26.el5_9.1


重新对python3.7进行编译安装，用一下过程来实现编译安装:

cd Python-3.7
./configure --with-ssl

make

sudo make install

2. Windows环境下：到```https://slproweb.com/products/Win32OpenSSL.html```上下载winopessl，直接下载第一个MSI安装即可：
