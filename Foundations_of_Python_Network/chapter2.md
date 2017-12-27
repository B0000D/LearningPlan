# 第2章 UDP

**多路复用(multiplexing)**:为两台主机间传送的大量数据包打上标签,区分标识网页的和用于电子邮件的数据包,同时与其他网络会话的数据包分隔开

**可靠传输(reliable transport)**:修复数据包流的传输错误;重传丢失的数据包;重组数据包的顺序;丢弃重复的数据包,防止信息冗余

**用户数据报协议(UDP)**:支持多路复用和分解,但是无法处理丢包,重包和包的乱序问题

**传输控制协议(TCP)**:解决了多路复用和可靠传输问题

有一些专用应用程序在IP上进行会话时,既不选择TCP协议,也不选择UDP协议

## 2.1 端口号

每个UDP数据包被分配了一对**无符号16位端口号**,端口号的范围是0~65536.

**源端口(source port)**:标识源机器上发送数据包的特定进程或程序

**目标端口(destination port)**:标识目标IP地址上进行该会话的特定应用程序

可以**同时使用ip地址和端口号**来标识源机器和目标机器,从而支持多个运行程序**互不影响**地同时进行交互

**Source(IP:port number)--->Destination(IP:port number)**

### 例



在IP地址为192.168.1.9的机器上假设一台DNS**服务器**,服务器向操作系统请求使用**53号端口(标准DNS端口)**的权限,用于接收发送至UDP端口的数据包

现在一台IP地址为192.168.1.30的**客户机**想要查询服务器.客户机在内存中构造该查询,然后请求操作系统将查询封装为一个**UDP数据包**并发送.目的地址的应用程序需要使用某种方法来识别发送该查询的客户端并返回响应,客户端没有显式声明请求端口号,操作系统会为该查询随机分配一个端口号,假设本例中随机分配了**44137号端口**.数据包的发送方式如下:

**Source(192.168.1.30:44137)---> Destination(192.168.1.9:53)**

DNS服务器构造完成了相应信息,就会请求操作系统发送一个UDP数据包作为响应,响应发回请求方的方式如下:

**Source(192.168.1.9:53)---> Destination(192.168.1.30:44137)**

客户端如何获悉需要连接的端口号呢?

**惯例**:互联网号码分配机构(IANA)为许多专用服务分配了官方的知名端口.比如DNS的默认端口为53号UDP端口

**自动配置**:计算机首次连接网络时,会使用DHCP这样的协议来获取一些重要服务的IP地址,比如DNS. 应用程序通过将这些IP地址与知名端口号结合,便可访问这些基础服务.

**手动配置**:除了上述两种情况,管理员或用户还必须手动配置IP地址或响应的服务域名.每当在网络浏览器中键入服务器名时,实际上都发生了手动配置的对应操作.

IANA为相应服务定义端口号时,将端口号分为三类(对UDP和TCP均适用)

**知名端口(0-1023)**:分配给最重要,最常用的服务.在很多类UNIX操作系统上,普通用户程序无法监听这些端口

**注册端口(1024~49151)**:在操作系统层上无特别之处.建议只在使用其指定服务时才使用这些端口.

**其余的端口号(49152~65535)**:可以随意使用,当客户端未指定特定端口时,系统便会从这些端口号中随机选取端口号用于该服务.

对用户友好的程序还应接收可读性较高的知名端口名,可用如下方法查询域名服务的端口号:

```python
import socket
servname=socket.getservbyname('domain')
print(servname)
```

```
53
```

在Linux和Mac机器上,知名服务名与其对应的端口号通常保存在/etc/services文件中.

IANA的在线版本:http://www.iana.org/assignments/port-numbers

## 2.2 套接字

网络操作背后的系统调用都是围绕这套接字这一概念来进行的

### 代码清单2-1 使用自环借口的UDP服务器和客户端

```python
`import argparse,socket
from datetime import datetime

MAX_BYTES=65535

#服务器端
def server(port):
    #创建一个空套接字 协议族AF_INET,数据报类型SOCK_DGRAM(使用UDP)
    sock=socket.socket(socket.AF_INET,socket.SOCK_DGRAM)
    #使用bind命令请求绑定一个UDP网络地址(IP地址字符串+整型的UDP端口号) 若端口已被占用则无法绑定
    sock.bind(('127.0.0.1',port))
    #可以使用getsockname()函数来获取包含IP地址和端口号的二元组
    print('Listening at {}'.format(sock.getsockname()))
    #循环运行recvfrom()
    while True:
        data,address=sock.recvfrom(MAX_BYTES)
        text=data.decode('ascii')
        print('The client at {} says {!r}'.format(address,text))
        text='Your data was {} bytes long'.format(len(data))
        data=text.encode('ascii')
        sock.sendto(data,address)


def client(port):
    sock=socket.socket(socket.AF_INET,socket.SOCK_DGRAM)
    text='The time is {}'.format(datetime.now())
    data=text.encode('ascii')
    sock.sendto(data,('127.0.0.1',port))
    print('The OS assigned me the address {}'.format(sock.getsockname()))
    data,address=sock.recvfrom(MAX_BYTES)
    text=data.decode('ascii')
    print('The server {} replied {!r}'.format(address,text))

if __name__=='__main__':
    choices={'client':client,'server':server}
    parser=argparse.ArgumentParser(description='Send and receive UDP locally')
    parser.add_argument('role',choices=choices,help='which role to play')
    parser.add_argument('-p',metavar='PORT',type=int,default=1060,help='UDP port (default 1060)')
    args=parser.parse_args()
    function=choices[args.role]
    function(args.p)
```

