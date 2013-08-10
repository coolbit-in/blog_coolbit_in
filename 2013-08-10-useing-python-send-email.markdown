---
layout: post
title: "使用python的stmplib模块来发邮件"
date: 2013-08-10 17:44
comments: true
categories: python
---
如果能让服务器远程发邮件，那是再好不过了。万能的python可以做到这一点，而且并不是很复杂，只需要`stmplib`模块来连接stmp服务器，用`email`模块来格式化email文件结构。大体上又两种格式化的方法，一种是使用`email.message.Message`这个类，还有就是更先进的`email.mime.text.MIMEText`这个类。

###使用 email.message.Message 的方法
```python
#!/usr/bin/env python
# -*- coding:utf=8 -*-
import smtplib
from email.message import Message
from time import sleep

smtpserver = 'smtp.gmail.com'
username = 'example_1@gmail.com'
password = '******'
from_addr = 'example_1@gmail.com'
to_addr = 'example_2@gmail.com'

my_message = Message()                      #建立Message实例
my_message['Subject'] = 'Useing Python'     #写邮件的标题
my_message['From'] = from_addr
my_message['To'] = to_addr
my_message.set_payload('The texts send success!')   #这里添加正文
msg = my_message.as_string()                        #将数据结构格式化成字符串

smtp_con = smtplib.SMTP(smtpserver, port = 587, timeout = 20)   #连接smtp服务器
smtp_con.set_debuglevel(1)                  #打开debug功能
#smtp_con.ehlo()                            #这行代码的作用是向服务器表明你自己的hostname，从文档上看这句话并不是必须的，login方法也可以完成这个功能
smtp_con.starttls()                         #使用ssl方式，gmail是强制使用的
smtp_con.login(username, password)          #登录
smtp_con.sendmail(from_addr, to_addr, msg)  #发信
sleep(5)
smtp_con.quit()
```
###使用 email.mime.text.MIMEText 的方法
```python
#!/usr/bin/env python
# -*- coding:utf=8 -*-
import smtplib
#from email.message import Message
from email.mime.text import MIMEText
from email.header import Header
from time import sleep

smtpserver = 'smtp.gmail.com'
username = 'example_1@gmail.com'
password = '******'
from_addr = 'example_1@gmail.com'
to_addr = 'example_2@gmail.com'

#使用 email.mime.text 的方式

'''发送文本
my_message = MIMEText('你好','plain','utf-8') #普通文字使用plain
my_message['Subject'] = Header('你好','utf-8') #Subject如果是中文需要转成utf-8
msg = my_message.as_string()
'''
#发送html
fp = open('text.html', 'r')
my_message = MIMEText(fp.read(), 'html', 'utf-8')
fp.close();
my_message['Subject'] = Header('尝试发送html', 'utf-8')
my_message['From'] = from_addr
my_message['To'] = to_addr
msg = my_message.as_string()

smtp_con = smtplib.SMTP(smtpserver, port = 587, timeout = 20)
smtp_con.set_debuglevel(1)
#smtp_con.ehlo()
smtp_con.starttls() #使用ssl方式，gmail是强制使用的
smtp_con.login(username, password)
smtp_con.sendmail(from_addr, to_addr, msg)
sleep(5)
smtp_con.quit()
```
其实`email.mime`能做的事情更多，例如发图片，视频，附件等，可以查看相关的文档。