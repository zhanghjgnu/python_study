## 在win10X64下pycharm venv环境安装lxml
安装其他package直接在pycharm IDE下用快捷键ALT+Shift+Enter爽得飞起，不过使用也是很常用的lxml的时候安装失败，查看失败details是cl.exe文件失败，lxml底层是用c写的，应该是和我安装的visual studio2019冲突吧，不能改变vs，只能想其他办法安装pycharm环境下的lxml了。解决办法如下:
1. 到https://www.lfd.uci.edu/~gohlke/pythonlibs/下载本机环境对应的whl包，我的是lxml_4.5.0_cp38_cp38_win_amd64.whl
2. 拷贝lxml?4.5.0?cp38?cp38?win_amd64.whl到$APP\venv\Lib\site-packages目录，运行安装程序
```
pip install  lxml-4.5.0-cp38-cp38-win_amd64.whl
```
3. 提示安装成功了，不过当前目录并没有生成lxml目录，pycharm环境下还是提示包不存在。仔细查看安装输出信息，结果安装到了d:\python\python38\lib\site-packages (4.5.0),也就是我python的主目录，把主目录下生成的两个目录lxml和lxml-4.5.0.dist-info拷贝到APP\venv\Lib\site-packages目录下，问题解决。
