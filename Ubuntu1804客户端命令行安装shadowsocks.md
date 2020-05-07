## Ubuntu1804客户端命令行安装shadowsocks

为了使用tensorflow必须实现科学上网,windows下gui客户端不用说了，linux命令行窗口安装客户端需要做一些必要的设置
1 安装 
    sudo apt install shadowsocks -y
2 配置客户端
    sudo nano /etc/shadowsocks.json
    配置文件参数如下:
 {
    "server":"1.1.1.1",
    "server_port":8383,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"passwd",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": true,
    "workers": 1
  }

3 启动代理服务器
  先用命令行验证 sudo sslocal -c /etc/shadowsocks.json 
  如果没问题了
  nohup sslocal -c /etc/shadowsocks.json &
  这样就成了后台启动

4 设置让终端走代理
    shadowsocks走的事socket5协议，需要设置环境变量
    export http_proxy="socks5://127.0.0.1:1080"
    export https_proxy="socks5://127.0.0.1:1080"
    或者干脆直接设置ALL_PROXY
    export ALL_PROXY=socks5://127.0.0.1:1080
5 验证代理是否设置成功
   curl https://www.google.com
