关于把python的fastapi项目docker化的过程

做个基于fastapi的后台应用，在pycharm环境下运行正常，只要在terminal下运行uvicorn main:app --reload，启动web服务后一切正常，遂决定给部署到服务器的docker容器中，过程一波三折。
服务器环境是ubuntu18.04 server,安装python步骤如下(主要是为了调试，运行docker下用不到的):
$ sudo apt-get update
$ sudo apt-get install python3.6
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:deadsnakes/ppa
$ sudo apt-get update
$ sudo apt-get install python3.8

1 建立目录/home/zhanghj/disk/python_userauth，这个目录做为项目的根目录。刚开始想用python镜像来做，结果发现了tiangolo/uvicorn-gunicorn-fastapi镜像，一下子能省不少事，镜像地址镜像地址https://hub.docker.com/r/tiangolo/uvicorn-gunicorn-fastapi

在这个目录下建立Dockerfile文件，内容如下:

FROM tiangolo/uvicorn-gunicorn:python3.8

LABEL maintainer="zhanghjgnu <330956988@qq.com>"

WORKDIR /home/zhanghj/disk/python_userauth
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY ./app /app

一定注意RUN pip install --no-cache-dir -r requirements.txt这句，文档上没有-r参数，一直提示
ERROR: Could not find a version that satisfies the requirement requirements.txt (from versions: none)
ERROR: No matching distribution found for requirements.txt
困扰了好久

2 这里应该说一下生成requirements.txt文件的过程，一定要在pycharm的terminal模式下，如果cmd进入dos模式，这个是全局模式，不是虚拟环境，生成的文件是有问题的。生成这个文件有两种方法:
方法1:
    pip freeze > requirements.txt
方法二：
    使用pipreqs生成requirements.txt
    先安装pipreqs
    pip3 install pipreqs
    生成requirements.txt
    pipreqs ./ --encoding=utf-8
    当项目里存在requirements .txt文件时，执行会提示一下警告，use --force to overwrite it,执行pipreqs ./ --encoding=utf-8 --force即可
区别:
    pip freeze导出Python环境下所有安装的类库
    pipreqs导出项目中使用的类库。

3 关于读取文件的路径，我使用了ConfigParser包,用一个database.ini文件来存储数据库的配置信息，在pycharm下运行正常，docker以后就提示找不到文件，原来的写法
def config_pg(filename='database.ini', section='postgresql'):
    # create a parser
    parser = ConfigParser()
    # read config file
    parser.read(filename)

    # get section, default to postgresql
    db = {}
    if parser.has_section(section):
        tparams = parser.items(section)
        for param in tparams:
            db[param[0]] = param[1]
    else:
        raise Exception('Section {0} not found in the {1} file'.format(section, filename))

    return db

找不到路径就给个绝对路径，改进的写法
def config_pg(filename='database.ini', section='postgresql'):
    # create a parser
    parser = ConfigParser()
    # read config file
    parser.read("%s/database.ini" % os.path.dirname(os.path.abspath(__file__)))
    #print(os.path.dirname(os.path.abspath(__file__)))
    #parser.read(filename)

    # get section, default to postgresql
    db = {}
    if parser.has_section(section):
        tparams = parser.items(section)
        for param in tparams:
            db[param[0]] = param[1]
    else:
        raise Exception('Section {0} not found in the {1} file'.format(section, filename))

    return db

4.打包及运行
docker build -t py_userauth .
docker run -d --name py_userauth_container -p 8090:80 py_userauth

5 测试，前端打开http://ip:8090/docs,认证登录,查看docker日志
docker logs py_userauth_container
