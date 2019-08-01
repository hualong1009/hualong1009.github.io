---
layout:     post
title:      Python3接收消息并转发到微信helper
subtitle:   使用python3编写程序实现消息中转
date:       2019-07-30
author:     Hansen Wang
header-img: img/post-bg-python_1.jpg
catalog: true
tags:
    - Python
    - 微信
---

## Python3 实现消息转发到微信helper

使用Python3编写程序，实现从TCP套接字接收消息，并转发给微信helper。可以做一些监控类的辅助。

## 应用场景

处理来自测试端的消息。测试端将测试状态，如测试完成的进度百分比，测试结果等消息，发给本程序运行的HOST端，host端收到消息后转发给微信登陆的helper用户。

## 程序内容

```python
import itchat, socket, time, optparse, threading

def getarguments():
    global OS_IP
    global OS_Port
    parser = optparse.OptionParser()
    parser.add_option('--host',dest='IP',type='string',help='host IP eg. --host 192.168.XX.XX')
    parser.add_option('--port',dest='PORT',type='int',help='host port eg. --port 54321')
    opts,args = parser.parse_args()
    OS_IP=opts.IP
    OS_Port=opts.PORT

def tcplink(sock, addr):
    print('Accept new connection from %s:%s...' % addr)
    while True:
        data = sock.recv(1024)
        time.sleep(1)
        if not data:
            break
        elif data.decode('utf-8').split("|")[0] == 'exit':
            exit()
        else:
            itchat.send("[%s]\nReceive msg from %s\n [Theme]: %s\n[Content]: %s" %(time.ctime(), addr[0], data.decode('utf-8').split("|")[0], data.decode("utf-8").split("|")[1]), toUserName="filehelper")
    sock.close()
    print('Connection from %s:%s closed.' % addr)

def main():
    global OS_IP, OS_Port
    getarguments()
    itchat.auto_login(enableCmdQR=True)
    Socket_R=socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    Socket_R.bind((OS_IP, OS_Port))
    Socket_R.listen(5)
    while True:
        sock, addr = Socket_R.accept()
        t = threading.Thread(target=tcplink, args=(sock, addr))
        t.start()

if __name__ == "__main__":
    main()
```

* `getarguments`设置运行时参数，设定host's ip and port
* `tcplink`设置套接字接收消息并转发给微信端
* `main`写了微信登陆，其实就是调用`itchat`模块

[![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMjg1NTc3OC05ZDc4NjEwZDFmOWM0MjFhLmpwZw)](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMjg1NTc3OC05ZDc4NjEwZDFmOWM0MjFhLmpwZw)

**推荐关注博主公众号，获取最新的文章😀**

[![服务器测试与运维](https://i.loli.net/2019/08/01/5d42e3a801fb564745.jpg)](https://i.loli.net/2019/08/01/5d42e3a801fb564745.jpg)

> 📌转载请注明来源，版权归作者**[@hualong1009](https://hualong1009.github.io)**所有, 谢谢