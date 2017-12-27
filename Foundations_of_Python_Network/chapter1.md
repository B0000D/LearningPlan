# 第1章 客户端/服务器网络编程简介

## 关于if __name__=='__main__':的意义:

一个python文件由两种使用方法:

1.直接作为脚本执行

2.import到其他的python脚本中被调用(模块重用)执行

if __name__=='__main__':可以控制这两种情况的执行:

在if __name__=='__main__':下的代码只有在第一种情况下(文件作为脚本直接执行)也会被执行,import到其他脚本中不会被执行.

## 1.1 协议栈与库

**协议栈**:复杂的网络服务建立在简单网络服务的基础上

**Python库** :包括python内置的标准库和下载安装的第三方库,用于解析要使用的**网络通信协议**

**网络编程**:选择并使用一个已经支持所需网络操作的库的过程

**python的标准库文档**

http://docs.python.org/3/library/

Doug Hellmann的博客 **Python Module of the Week**

Python包索引(Python Package Index)

### **virtualenv**

创建任意数量的小型,独立的"虚拟Python环境"

保护系统Python环境(无需修改)

**安装virtualenv**

[Pycharm配置virtualenv]: http://www.linuxidc.com/Linux/2016-12/137838.htm

### 代码清单1-1 获取经度和纬度(使用第三方库pygecoder)

```python
from pygeocoder import Geocoder
if __name__=='__main__':
    address='207 N.Defiance St,Archbold,OH'
    print(Geocoder.geocode(address)[0].coordinates)
```

```
(41.5219645, -84.3066496)
```

## 1.2 应用层



使用更底层的**requests**库为谷歌地图api编写客户端

### 代码清单1-2 从谷歌地理编码api获取一个json文档

```python
import requests
def geocode(address):
    # 需要查询的邮寄地址是由名为address的参数标识的,另一个参数说明该位置查询并非源于移动设备位置传感器提供的实时数据
    parameters={'address':address,'sensor':'false'} 
   #构造基础url
base='http://maps.googleapis.com/maps/api/geocode/json'
    #该url的查询结果
    response=requests.get(base,params=parameters)
    #将结果转化为JSON格式
    answer=response.json()
    #通过python字典的多层取值获取经度和纬度
    print(answer['results'][0]['geometry']['location'])	
if __name__=='__main__':
    #调用geocode函数
    geocode('207 N.Defiance St,Archbold,OH')
    
```

```
{'lat': 41.5219645, 'lng': -84.3066496}
```

程序输出与第一段代码的输出**并非完全相同**

JSON数据将结果封装为一个"对象",requests库以python字典的形式提供了该"对象"

**目的**:查询一个邮寄地址对应的经纬度.

**构造url-->获取查询响应-->将结果转化为json**

**高层代码描述查询的意义,底层代码展示查询的构造细节**

## 1.3 协议的使用

#### url

描述了网络上特定文档的位置以及获取方法

包含协议名称 + 保存文档的主机名 + 该主机上特定文档的路径

#### http:超文本传输协议

### 代码清单1-3 使用原始http操作连接谷歌地图

```python
import http.client
import json
from urllib.parse import quote_plus

base='/maps/api/geocode/json'

def geocode(address):
    #在问号后跟上由&分隔的参数(name=value的形式) 
    path='{}?address={}&sensor=false'.format(base,quote_plus(address))
   #请求一台特定的主机
connection=http.client.HTTPConnection('maps.google.com')
    #手动构造一个带path参数的get查询
    connection.request('GET',path)
    #从http连接读取响应结果
    rawreply=connection.getresponse().read()
    #将查询参数手动嵌入到查询地址中
    reply=json.loads(rawreply.decode('utf-8'))
    print(reply['results'][0]['geometry']['location'])

if __name__=='__main__':
    geocode('207 N.Defiance St,Archbold, OH')

```

```
{'lng': -84.3066496, 'lat': 41.5219645}
```

本例使用的协议比起前面的程序在协议栈中更低了一层

#### 1.4 一个原始的网络会话

http必须使用一些更简单的抽象来完成操作

**HTTP协议精确描述了两台主机间通过TCP传输的信息格式,并以此提供HTTP的各项功能**

### 代码清单1-4 直接使用套接字与谷歌地图通信

```python
import socket
from urllib.parse import quote_plus

#GET信息后跟着一些请求头,每个请求头包含了名称,冒号,值,最后是表示请求结束的回车符和换行符
request_text= """\
GET /maps/api/geocode/json?address={}&sensor=false 
HTTP/1.1\r\n\
#关键字GET 待获取文档的路径以及支持的HTTP版本
Host:maps.google.com:80\r\n\
User-Agent:search4.py(Foundations of Python Network Programming)\r\n\
Connection:close\r\n\
\r\n\
"""


def geocode(address):
    sock=socket.socket()
    sock.connect(('maps.google.com',80))
    request=request_text.format(quote_plus(address))
    #sendall()函数传入HTTP查询的具体内容
    sock.sendall(request.encode('ascii')) 
    raw_reply=b''
    while True:
        more=sock.recv(4096)
        if not more:
            break
        raw_reply+=more
    print(raw_reply.decode('utf-8'))

if __name__=='__main__':
    geocode('207 N.Defiance St,Archbold,OH')

```

```
#打印响应字节串

#状态行和一些响应头
HTTP/1.1 200 OK
Content-Type: application/json; charset=UTF-8
Date: Tue, 26 Dec 2017 08:13:55 GMT
Expires: Wed, 27 Dec 2017 08:13:55 GMT
Cache-Control: public, max-age=86400
Access-Control-Allow-Origin: *
Server: mafe
X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
Accept-Ranges: none
Vary: Accept-Language,Accept-Encoding
Connection: close
#响应体(json格式 查询的响应结果)
#这里描述了谷歌地理编码api返回的地理位置
{
   "results" : [
      {
         "address_components" : [
            {
               "long_name" : "207",
               "short_name" : "207",
               "types" : [ "street_number" ]
            },
            {
               "long_name" : "North Defiance Street",
               "short_name" : "N Defiance St",
               "types" : [ "route" ]
            },
            {
               "long_name" : "Archbold",
               "short_name" : "Archbold",
               "types" : [ "locality", "political" ]
            },
            {
               "long_name" : "German Township",
               "short_name" : "German Township",
               "types" : [ "administrative_area_level_3", "political" ]
            },
            {
               "long_name" : "Fulton County",
               "short_name" : "Fulton County",
               "types" : [ "administrative_area_level_2", "political" ]
            },
            {
               "long_name" : "Ohio",
               "short_name" : "OH",
               "types" : [ "administrative_area_level_1", "political" ]
            },
            {
               "long_name" : "United States",
               "short_name" : "US",
               "types" : [ "country", "political" ]
            },
            {
               "long_name" : "43502",
               "short_name" : "43502",
               "types" : [ "postal_code" ]
            },
            {
               "long_name" : "1160",
               "short_name" : "1160",
               "types" : [ "postal_code_suffix" ]
            }
         ],
         "formatted_address" : "207 N Defiance St, Archbold, OH 43502, USA",
         "geometry" : {
            "bounds" : {
               "northeast" : {
                  "lat" : 41.521994,
                  "lng" : -84.30646179999999
               },
               "southwest" : {
                  "lat" : 41.521935,
                  "lng" : -84.30683739999999
               }
            },
            "location" : {
               "lat" : 41.5219645,
               "lng" : -84.3066496
            },
            "location_type" : "ROOFTOP",
            "viewport" : {
               "northeast" : {
                  "lat" : 41.5233134802915,
                  "lng" : -84.30530061970849
               },
               "southwest" : {
                  "lat" : 41.5206155197085,
                  "lng" : -84.3079985802915
               }
            }
         },
         "place_id" : "ChIJk4BHnIy0PYgRXbKj5GjFe_U",
         "types" : [ "premise" ]
      }
   ],
   "status" : "OK"
}

```

之前3个程序各自都使用了一个Python库,这些库用于解析复杂的网络协议

本例中已经深入到了最底层:**使用主机操作系统提供的原始socket()函数来支持IP网络上的网络通信**

## 1.5 层层深入

使用python进行网络编程的**几个要点**:

### 协议栈

前面例子中的协议栈包括4层:

1.**最上层的谷歌地理编码api**,对**如何用url表示地理信息查询**和 **如何获取包含坐标信息的json数据**进行了 **封装**

2.**URL**:标识了可通过HTTP获取的文档

3.**HTTP层**:支持面向文档的命令(例如GET) 该层的操作使用了原始TCP/IP套接字

4.**TCP/IP套接字**:只处理字节串的发送和接收

### 一.协议栈的每一层都使用了其底层协议提供的功能,同时向上层协议提供服务

### 二.Python对我们涉及的各网络层都提供了非常全面的支持

### 三.随着使用的通信协议越来越底层,程序的质量也明显随之下降-->因此应该尽可能地使用标准库或第三方库来实现网络协议

### 四.高层的网络协议通常会讲其底层网络细节隐藏

### 五.socket()接口并非最底层的协议

**socket()API层之下的几层**

**传输控制协议(TCP)**:通过发送(可能重发),接收以及重排称为数据包的小型网络信息,支持由字节流组成的双向网络会话

**网际协议(IP)**:处理不同计算机间数据包的发送

**最底层的"链路层"**:负责在直接相连的计算机之间发送物理信息,由网络硬件设备组成,如以太网端口和无线网卡.

## 1.6 编码与解码

### Python的字符串和字节

**字节(Byte)**:计算机网络通信中实际传输的二进制数,每个字节由8位二进制数构成

**字符(Character)**:包含了Unicode字符

在Python3中,除非使用者主动请求转化,对使用者可见的只有**字符**

### 解码和编码

**解码(decoding)**:应用程序需要理解字节的意义.接收到字节时,程序对通信信道间传输的原始字节进行解密.将字节转化为字符串. **使用decode()方法将读入的字节串转化为字符串**

**编码(encoding)**:程序将字符串对外输出,使用编码方法将字符串转化为字节 **使用encode()方法对要输出的字符串进行编码**

### 代码清单1-6 解码输入字节,编码输出字符

```python
if __name__=='__main__':
    input_bytes=b'\xff\xfe4\x001\x003\x00 \x00i\x00s\x00 \x00i\x00n\x00.\x00'
    input_characters=input_bytes.decode('utf-16')
    print(repr(input_characters))

    output_characters='We copy you down, Eagle.\n'
    output_bytes=output_characters.encode('utf-8')
    with open('eagle.txt','wb')as f:
        f.write(output_bytes)
```

```
'413 is in.'
```

注意调用repr()方法的区别:

字节串由字母b开始 b'hello'

字符串没有起始字母 'world'

Python3只对字符串类型提供了大量的字符串方法

## 1.7 网际协议(IP)

**网络互联(networking)**:通过物理链路将多台计算机连接,使之可以相互通信

**网际互联(internetworking)**:将相邻的物理网络相连,使之形成更大的网络系统(如互联网)

**数据包(packet)**:网络设备间进行共享和数据传输的基本单元 一个数据包是一串长度在几字节到几千字节间的字节串

**数据包的两个属性**:**包含的字节串数据** **目标传输地址**

物理数据包的地址一般是一个唯一的标识符

网卡负责发送并接收这样的数据包

**网际协议(IP)**:为全世界通过互联网连接的计算机赋予**统一地址系统**的一种机制 使得数据包能够从互联网的一段发送至另一端

## 1.8 IP地址

IP的最初版本为连接到万维网的每台计算机分配了一个4字节的地址,通常写为**4个由句点分隔的十进制数.每个十进制数表示地址的1字节**.每个数的范围是0到255

形如:130.207.244.244

**主机名(hostname)**:一般用来代替IP地址 便于人们记忆 可以解析为IP地址

访问主机名之后,首先将主机名解析到IP地址,计算机实际上是通过互联网将数据包传输到了该地址

### 代码清单1-7 将主机名转换为IP地址

```python
import socket

if __name__=='__main__':
    hostname='www.baidu.com'
    addr=socket.gethostbyname(hostname)
    print('The IP address of {} is {}'.format(hostname,addr))

```

```
The IP address of www.baidu.com is 103.235.46.39
```

该程序用来向运行程序的操作系统请求解析主机名www.baidu.com，支持Linux Mac OS Windows或其它任何系统。

这个特定的网络服务叫做 **域名系统（DNS）**

### 两个要点：

### 1.IP协议总是使用数字标识的IP地址来作为数据包传输的目标地址

### 2.将主机名解析为IP地址这一复杂的细节是由操作系统来处理的

### 如今地址表示的问题

全球的4字节IP地址已经开始不够用了，人们又部署了一种名为 **IPV6** 的扩展地址机制。

**IPV6**：允许使用16字节的地址 写法形如：**fe80::fcfd:4aff:fecf:ea4e**

运行Python代码的操作系统会知道使用的IP版本 并作出相应的解析

传统的IP地址 前1~2个字节表示某个机构，接下来的字节通常标识目标机器所在的特定子网，最后一个字节将地址细化至该特定的机器或服务。

**特殊的IP地址段**：

### **127.\*.\*.\***

以127开头的IP地址是特殊的预留地址段,由机器上运行的本地应用程序使用.

连接到这一地址段中的地址时,其实是在与统一机器上的一些其他服务或程序交互.

多数机器只是用这一地址段中的一个地址:127.0.0.1 表示"运行该程序的机器本身",通常可以通过主机名localhost来访问

### **10.\*.\*.\*   172.16-31.\*.\*  192.168.\*.\***

这些IP地址段是为 **私有子网**预留的 

连接互联网时可以确定,这些地址是没有意义的,并不对应可连接的任一主机.

如果要构建组织内部网络，可以随意使用这些地址来自由分配内部的IP地址，不需让外网访问这些主机。

## 1.9 路由

**路由（routing）**：根据目的IP地址选择将IP数据包发往何处

### 3种情况：（对于运行在网络边缘的程序）

**IP地址形如127.\*.\*.\***：目的地址是本机上运行的另一个应用程序。数据包不会被传送给物理网络设备，而是直接通过操作系统的内部数据复制转交给另一应用程序。

**目的IP地址和本机处于同一子网**：通过简单地检查本地以太网段、无线信道或是其他任何网络信息来找到目标主机，然后将数据包发送给本地连接的机器

**否则**：将数据包转发给一台网关机器。这台机器将本地子网连接至互联网，然后再决定将数据包发往何处

对于组成互联网主干网的专用网络设备，路由决定要复杂的多。需要使用 **交换机**将整个网络的各子网相连。在交换机中构建、查询并不断更新详细的 **路由表**

### 结合IP地址和掩码来表示子网

**127.0.0.0/8**：地址的前8位必须与127匹配，余下的24位可以是任意值

**192.168.0.0/16**：匹配了属于192.168私有地址段的任何IP地址，其中前16位必须完全匹配

**192.168.5.0/24**：这里明确指定了一个特定的独立子网。一种常见的子网掩码，属于该子网的机器只有最后一字节不同，允许有256个不同的地址。通常，**.0地址**用来表示子网名,**.255地址**则用作"**广播数据包**"的目标地址.**广播数据包**会被发送到子网内的所有主机.**.1地址**通常用于连接外网的网关

## 1.10 数据包分组

构建于IP网络之上的实际网络设备通常并不支持这么大的数据包 

因此分组是很有必要的

网络数据包中包含一个表示 **不分组(DF,Don't Fragment)**的标记,在源计算机与目的计算机之间的某条物理网络无法容纳太大的数据包时,发送者可以通过这个标记**选择是否进行分组**

**没有设置DF标记**:**允许分组**

数据包大小超过网络能够容纳的上限时,网关能够将其分为多个小数据包,并进行标记,表示接收方在接受之后需要将这些小数据包重组为原始大数据包

**设置DF标记**: **不允许分组** 网络如果无法容纳数据包,将会丢弃该数据包,并发回一条错误信息.,由**Internet控制报文协议(ICMP, Internet Control Message Protocol)数据包**表示.发送方在收到错误信息后会尝试将信息分割为较小的数据包,然后重发

DF标记通常由操作系统来设置.

> 正在进行由网络间传输的独立数据包组成的**UDP会话**
>
> 不会设置DF标记,使得所有数据都能到达接收方
>
> 

> 正在进行 **TCP会话**
>
> 是由可能多大成百上千的数据包组成的长数据流,将设置
> DF标记,使TCP会话顺畅进行.否则数据包会不断分组,从而降低会话效率

**最大传输单元(MTU)**:一个互联网子网能够接收的最大数据包

## 1.11 进一步学习IP

由IETF发布的RFC文档

**http://tools.ietf.org/html/rfc791**

TCP/IP详解(卷1):协议(第2版)

## 1.12 小结

### **TCP/IP协议**支持在客户端和服务器之间传输字节串

### **HTTP协议**描述了客户端如何通过TCP/IP建立的连接来请求特定的文档,以及服务器如何响应并提供相应的结果

### **万维网**将获取由HTTP托管的文档所需的指令编码为一个特殊的地址(**URL**)

### **JSON**格式是最流行的表示返回文档的格式

### 谷歌提供了一个地理编码服务,程序员构建一个URL用于请求,谷歌会返回一个描述地理位置的JSON文档

### 传输或存储文本信息需要将字符编码为字节,可以使用**ascii编码**或者unicode编码**UTF-8**         Python3需要经常使用**decode()**和**encode()**方法

### **IP网络**帮助应用程序传输数据包

### 单独的机器被分配**IP地址**,IP地址和用户可见的**域名**相关联

### 如果数据包超过大小限制,可能会进行**分组**

### 有两种使用IP的基本方法:

**UDP   1.将每个数据包视为独立的信息来使用**

**TCP    2.请求一个被自动分为多个数据包的数据流**



