运行pip install -e git+https://github.com/cocodataset/cocoapi#egg=pycocotools&subdirectory=PythonAPI
提示
error: RPC failed; curl 18 transfer closed with outstanding read data remaining
fatal: The remote end hung up unexpectedly
fatal: early EOF
fatal: index-pack failed

考虑可能网速问题，反复试了几次都是如此，google一下，发现遇到这个问题的人很多。解决办法有的说等网速好了再操作就行了，还有人说修改默认缓存值，既然不能等，就修改一下默认缓存吧，
原因认为我clone的文件过大，需要修改postBuffer默认缓存值
把postBuffer的值配置成500M
git config --global http.postBuffer 524288000

这样已经配置好了，可以根据以下命令查看postBuffer的修改结果:
   git config --list

顺便再说说这个pip install的-e参数:
我的环境是当前目录有一个requirements.txt，内容如下:
```
six
google-api-python-client>=1.6.7
google-cloud-bigquery>=0.31.0
kaggle>=1.3.9
mlperf_compliance==0.0.10
numpy>=1.15.4
oauth2client>=4.1.2
pandas>=0.22.0
psutil>=5.4.3
py-cpuinfo>=3.3.0
scipy>=0.19.1
tensorflow-hub>=0.6.0
tensorflow-model-optimization>=0.2.1
tensorflow-datasets
tensorflow-addons
dataclasses
gin-config
typing
sentencepiece
Cython
matplotlib
opencv-python-headless
pyyaml
Pillow
-e git+https://github.com/cocodataset/cocoapi#egg=pycocotools&subdirectory=PythonAPI
```
关注最后一行pip install -e 主要是指定额外要求的,做三件事
    installs site-packages/PackageName.egg-link file
    adds path to site-packages/easy-install.pth
    optionally installs CLI targets in <venv>/bin

从VCS安装

例：
```
pip install -e git+https://github.com/user/project.git#egg=project[extra]
pip install -e git+https://git.repo/some_pkg.git#egg=SomeProject???? # from git
pip install -e hg+https://hg.repo/some_pkg.git#egg=SomeProject? # from mercurial
pip install -e svn+svn://svn.repo/some_pkg/trunk/#egg=SomeProject?? # from svn
pip install -e git+https://git.repo/some_pkg.git@feature#egg=SomeProject? # from a branch
```
使用场景可以参考
```
https://stackoverflow.com/questions/42609943/what-is-the-use-case-for-pip-install-e/59667164#59667164?newreg=9c456c4fac1e46049b0174b263f67d0b
```
折腾了一番，发现下载还是出现这个错误，下面给出访问github慢的解决办法
1. 修改本地hosts文件
windows系统的hosts文件的位置如下：C:\Windows\System32\drivers\etc\hosts
linux系统的hosts文件的位置如下：/etc/hosts
2 增加http://github.global.ssl.fastly.net和http://github.com的映射
获取Github相关网站的ip
访问https://www.ipaddress.com/ip-to-country/
分别输入github.global.ssl.fastly.net和github.com，查询ip地址
下面是我的配置
140.82.113.3	github.com
199.232.69.194	github.global.ssl.fastly.net

修改后再次pip3 install -r requirements.txt，成功了。
