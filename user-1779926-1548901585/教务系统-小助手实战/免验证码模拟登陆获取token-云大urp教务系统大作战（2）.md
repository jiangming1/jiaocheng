##通过真实世界看程序世界
这里我们需要注意到的是我们的代码应该模拟着真实urp系统的运作，将其步骤进行抽象化代码化。
我们采用f12 开发者控制台来查看urp的真实登录运作：

![输入账号名密码验证码之后，交互情况](http://upload-images.jianshu.io/upload_images/1779926-47984bef9d4eca4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>上图从上向下三个分布为
1. 我们向urp的验证码接口信息接口请求了验证码的文件名。
2. 我们向vimgs图片分发接口请求了刚才规定我们请求的验证码
3. 我们向token验证接口post了我们提交的表单数据。


![步骤一请求](http://upload-images.jianshu.io/upload_images/1779926-e0c86614e5726b4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![步骤一返回值](http://upload-images.jianshu.io/upload_images/1779926-5304322bdd86381f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![步骤二请求值](http://upload-images.jianshu.io/upload_images/1779926-cbba4184d93b7c72.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**可以看出上图步骤二中的最后图片文件名为步骤一返回值中的imgguid**

![步骤二返回的验证码图片](http://upload-images.jianshu.io/upload_images/1779926-d124128005965bb9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![步骤三post请求](http://upload-images.jianshu.io/upload_images/1779926-876c8a15071e218d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![步骤三post请求携带的值](http://upload-images.jianshu.io/upload_images/1779926-ad5176d7c74c3ced.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**此处可看出提交的password字段：（密码|验证码图片真实值*步骤一返回的tempid）**

总结一下具体的流程图：

![总体流程.png](http://upload-images.jianshu.io/upload_images/1779926-b89e3f92e089fa7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###代码模拟：
分析好了真实流程我们只需要在代码里模拟一遍，并通过我们上一节中已经获取好的验证码表来进行登录。
模拟登录代码:
```
#!/usr/bin/env python
# coding:utf8
import sys
reload(sys)
sys.setdefaultencoding( "utf8" )
import json
import requests
from requests import exceptions

#1.The method to get accesstoken

def getToken(stucode,passwd):
	headers={'User-Agent':'Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13B143 Safari/601.1'}
	URL = 'http://202.203.209.96/v5api/api/GetLoginCaptchaInfo/f525199d-9d7b-4547-9094-1528bed37496'
	URL2 = 'http://202.203.209.96/v5api/OAuth/Token'
	fr = open('../data/dict.txt','r')
	dic = dict()
	for line in fr:
	    line = line.strip().split(',')
	    dic[line[0]] = line[1]
	fr.close()
	response = requests.get(URL,headers=headers)
	imgguid  = response.json()['ImgGuid']
	caputre  = dic[imgguid]
	tempguid = response.json()['TempGuid']
	response2 = requests.post(URL2,headers=headers,data={'grant_type':'password','username':'%s'%(stucode),'password':'%s|%s*%s'%(passwd,caputre,tempguid),'client_id':'ynumisSite'})
	# print response2.request.headers
	print response2.status_code
	errorcode =set()
	if(response2.status_code !=200):
		errorcode.add(imgguid)
		fw = open('error.txt','a')
		fw.write(str(imgguid)+'\n')
		fw.close()
	jsonobj = json.loads(response2.text)
	token =jsonobj['access_token']
	return token
stucode = '学号'
passwd = '密码'
token = getToken(stucode,passwd)
```
![拿到的token返回值](http://upload-images.jianshu.io/upload_images/1779926-5cf186d497e3a869.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![获取到token同时拿到的学生数据](http://upload-images.jianshu.io/upload_images/1779926-acd657eb71e00459.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

token值相当于服务器识别我们的一张身份令牌，我们在网线的这头，服务器在网线的那头，服务器只会通过token来识别我们的身份存在。没有token，服务器永远不知道坐在电脑那端的是条狗还是个人。

![没有token的小狗](http://upload-images.jianshu.io/upload_images/1779926-2042146e42f37fea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下一节我们将通过获取到的token，实现成绩查询，选课，退课，一键评教等功能。
