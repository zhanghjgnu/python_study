## windows10下安装pip
### 1.进入python官方网站，点击上方导航栏的PyPI，搜索框输入pip，点击search按钮，
在搜索列表中找到pip的最新版本pip 19.3.1.
在进入的Download files页面中，选择Source文件，扩展名为.tar.gz(不用whl的)，下载解压。
### 2.以管理员权限运行cmd,进入解压的目录，执行
```
cd D:\tools\python\pip-19.3.1\pip-19.3.1
python setup.py install
```
运行后提示
```
Installed c:\users\administrator\appdata\local\programs\python\python38\lib\site-packages\pip-19.3.1-py3.8.egg
Processing dependencies for pip==19.3.1
Finished processing dependencies for pip==19.3.1
```
说明安装好了。

### 3.与安装python时一样，我们希望命令行在任何目录下都能使用pip命令，所以需要配置环境变量，配置环境变量的过程与python一样，将python安装目录下的script文件的路径加入到Path中
我的python安装路径为 
```
C:\Users\Administrator\AppData\Local\Programs\Python\Python38
```
所以需要增加PATH 
```
C:\Users\Administrator\AppData\Local\Programs\Python\Python38\Scripts
```
### 4.验证安装版本
```
pip -V
pip 19.3.1 from c:\users\administrator\appdata\local\programs\python\python38\lib\site-packages\pip-19.3.1-py3.8.egg\pip (python 3.8)
```
