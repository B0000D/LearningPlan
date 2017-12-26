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
    parameters={'address':address,'sensor':'false'}
    # 需要查询的邮寄地址是由名为address的参数标识的,另一个参数说明该位置查询并非源于移动设备位置传感器提供的实时数据
    base='http://maps.googleapis.com/maps/api/geocode/json'
    #构造基础url
    response=requests.get(base,params=parameters)
    #该url的查询结果
    answer=response.json()
    #将结果转化为JSON格式
    print(answer['results'][0]['geometry']['location'])
	#通过python字典的多层取值获取经度和纬度
if __name__=='__main__':
    geocode('207 N.Defiance St,Archbold,OH')
    #调用geocode函数
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
    path='{}?address={}&sensor=false'.format(base,quote_plus(address))
   #在问号后跟上由&分隔的参数(name=value的形式) 
connection=http.client.HTTPConnection('maps.google.com')
    #请求一台特定的主机
    connection.request('GET',path)
    #手动构造一个带path参数的get查询
    rawreply=connection.getresponse().read()
    #从http连接读取响应结果
    reply=json.loads(rawreply.decode('utf-8'))
    #将查询参数手动嵌入到查询地址中
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

request_text= """\
GET /maps/api/geocode/json?address={}&sensor=false HTTP/1.1\r\n\
Host:maps.google.com:80\r\n\
User-Agent:search4.py(Foundations of Python Network Programming)\r\n\
Connection:close\r\n\
\r\n\
"""

def geocode(address):
    sock=socket.socket()
    sock.connect(('maps.google.com',80))
    request=request_text.format(quote_plus(address))
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