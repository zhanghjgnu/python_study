## 安装pyspider过程总结
### 本地环境为win10X64,python3.8.0
### 1.安装pyspider错误Please specify --curl-dir=/path/to/built/libcurl
从https://www.lfd.uci.edu/~gohlke/pythonlibs/下载 pycurl，版本选择pycurl-7.43.0.3-cp38-cp38-win_amd64.whl，我的环境是win10x64,python3.8.0
pip install pycurl-7.43.0.3-cp38-cp38-win_amd64.whl
然后再安装pyspider
pip install pyspider

安装提示成功。
### 2.然后验证一下 pyspider all，结果又出错了:
Traceback (most recent call last):
  File "c:\users\administrator\appdata\local\programs\python\python38\lib\runpy.py", line 192, in _run_module_as_main
    return _run_code(code, main_globals, None,
  File "c:\users\administrator\appdata\local\programs\python\python38\lib\runpy.py", line 85, in _run_code
    exec(code, run_globals)
  File "C:\Users\Administrator\AppData\Local\Programs\Python\Python38\Scripts\pyspider.exe\__main__.py", line 4, in <module>
  File "c:\users\administrator\appdata\local\programs\python\python38\lib\site-packages\pyspider\run.py", line 231
    async=True, get_object=False, no_input=False):
    ^
SyntaxError: invalid syntax

原因是从python 3.7开始async和await已经加入保留的关键字中，所以async不能最为函数的参数名
打开目录C:\Users\Administrator\AppData\Local\Programs\Python\Python38\Lib\site-packages\pyspider\,
使用Notepad++将run.py中的async统一改为async1，还有两个文件：
fetcher\tornado_fetcher.py
webui\app.py
也同样去改

### 3. 重新运行pyspider all
这次提示:
c:\users\administrator\appdata\local\programs\python\python38\lib\site-packages\pyspider\libs\utils.py:196: FutureWarning: timeout is not supported on your platform. ...

原因是WsgiDAV发布了版本 pre-release 3.x导致，解决办法就是降低WsgiDAV版本，命令窗口运行
python -m pip install wsgidav==2.4.1

然后pyspider all，发现app和5000端口被启用了，访问http://localhost:5000，发现也ok了
