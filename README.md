# Python-Crawler-Notes
VPN 模拟登录

# 抓取post数据
```
Scheme: http
Host: 162.105.67.92
Filename: /dana-na/auth/url_kAIEt6Xw70UNHaL0/login.cgi
```

```
username	"2101111539"
password	"2101111539@pku"
realm	"pku's+users"
btnSubmit	"登录"
```



# code

```python
#-*-coding:utf-8-*-
import os
import re
from lxml import etree
import requests
import sys
from bs4 import BeautifulSoup
from PIL import Image
# 初始参数，输入学号，密码。
# 2101111539/2101111539@pku
studentnumber = input("学号： ")
password = input("密码： ")

# 得到__VIEWSTATE的值。
s = requests.session()
#url = "http://162.105.67.92/dana-na/auth/url_kAIEt6Xw70UNHaL0/welcome.cgi"
#response = s.get(url)
url2 = "http://162.105.67.92/dana-na/auth/url_kAIEt6Xw70UNHaL0/login.cgi"
response = s.get(url)
selector = etree.HTML(response.content)
__VIEWSTATE = selector.xpath('//*[@id="form1"]/input/@value')[0]



# 构建post数据
data = {
"__VIEWSTATE":__VIEWSTATE,
"username":studentnumber,
"password":password,
"realm":pku's+users,
"btnSubmit":"",
}

# POST header，模拟浏览器访问网页。
headers = {
    "User-Agent":"Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.110 Safari/527.17",
}

# 登陆VPN
response = s.post(url,data=data,headers=headers)
print ("成功登录VPN")

# 得到登录信息
def getInfor(response,xpath):
    content = response.content.decode('gb2312') #网页源码是gb2312要先解码
    selector = etree.HTML(content)
    infor = selector.xpath(xpath)[0]
    return infor

# 获取基本信息
text = getInfor(response,'//*[@id="pkuid"]/text()')
text = text.replace(" ","")
print (text)

#获取课表，kburl是课表页面url,为什么有个Referer参数,这个参数代表你是从哪里来的。就是登录后的主界面参数。这个一定要有。
kburl = "http://162.105.67.92/baseinfo.cgi?xh="+studentnumber+"&amp;xm=%D5%C5%B8%DB&amp;gnmkdm=N121603"
headers = {
"Referer":"http://162.105.67.92/dana-na/auth/url_kAIEt6Xw70UNHaL0/login.cgi",
"User-Agent":"Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.110 Safari/527.17",
 }
response = s.get(kburl,headers=headers)

# html代表访问内部系统页面。下面解析这个页面。
html = response.content.decode("gb2312")
selector=etree.HTML(html)
content = selector.xpath('//*[@id="Table1"]/tr/td/text()')
for each in content:
   print (each)
```


# 已测试列表
```
2101111539/2101111539@pku
2101111560/1560@2021
2101111591/liwenqiang2021
2101111609/2021pku@qwe
2101111610/1qaz@wsx!
2101111670/1670Qwer!
2101111699/gaoyuting2003
2101111733/1733songxuan
```
