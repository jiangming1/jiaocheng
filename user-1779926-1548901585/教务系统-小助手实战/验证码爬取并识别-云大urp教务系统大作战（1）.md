##1. 穷举爬取urp的验证码
>因为小道消息得知urp的验证码是有限数量的，因此通过数百万次的请求，下载所有验证码图片到本地，并生成了一份验证码图片的文件名的文件。

爬取所有验证码图片的代码:
```
#encoding: utf-8  
import sys  
reload(sys)  
sys.setdefaultencoding('utf8')
#上述代码解决中文乱码问题 

import urllib2
import json
import requests
from requests import exceptions
##导入所需要的包

URL = 'http://202.203.209.96/v5api/api/xk/Captcha'
png_names = set()
#此处取巧使用set自动去重.
fw =open('png_name.txt','a')
#下载验证码同时保存图片名。

def basic_oauth():
	auth ='添加选课界面f12获取到的auth值'
	headers={'User-Agent':'Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13B143 Safari/601.1',
			'Authorization':'%s'%auth}
	try:
		num = len(png_names)
		response = requests.get(URL,headers=headers)
		print response.text
		png_names.add(response.text)

        #此处只有当前新获取的验证码是一张集合里并没有存在过的。
        #也就是引起了set长度变大。则将它才写入文件并下载保存。
        #切片[1:-1]去除掉双引号。
		if len(png_names)>num:
			c =response.text[1:-1]
			print "add"
			fw.write(response.text+'\n')

            #下面保存验证码图片到本地

			file =open('./captcha/'+str(c)+'.png','wb')
			req = urllib2.urlopen('http://202.203.209.96/vimgs/'+str(c)+'.png')
			buf = req.read()
			file.write(buf)

			print "**************"
			print ("第%d张正在保存"%len(png_names))
	except Exception as e:
		print e
		pass

for x in xrange(1,100):
	basic_oauth()
```

![爬取过程](http://upload-images.jianshu.io/upload_images/1779926-ab6c63caf244af75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![已爬取到本地的验证码](http://upload-images.jianshu.io/upload_images/1779926-3eedef50663d69f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**经统计urp真实验证码存量为999张。**

![urp验证码数量](http://upload-images.jianshu.io/upload_images/1779926-dcc5334806cbe400.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##2. 使用验证码识别api识别urp验证码
既然已经拿到了所有的验证码，我们对于验证码进行识别工作。
可以进行人工的肉眼识别，不过这样太过劳累，既然我们是python程序员，我们应该寻找一些偷懒的方法。（毕竟人生苦短）
**阿里云市场的验证码识别接口**
价格为：10元1000次
[图片验证码中英数接口api](https://market.aliyun.com/products/57124001/cmapi011148.html?spm=5176.730005.0.0.R1PsH1#sku=yuncode514800004)
###下载python版本sdk
```python
# -*- coding: utf-8 -*-
from com.aliyun.api.gateway.sdk.util import showapi
import json
import base64

# png_name.txt内我们在上一步爬取时保存的是图片名
fr = open('png_name.txt','r')
for line in fr:
	png_name = line.strip()
	f=open(r'./captcha/%s.png'%(png_name),'rb')
	b_64=base64.b64encode(f.read())
	f.close()
    #appcode 要改为自己购买后得到的appcode
	req=showapi.ShowapiRequest( "http://ali-checkcode.showapi.com/checkcode","appcode" )
	json_res= req.addTextPara("typeId","3000")\
	    .addTextPara("img_base64",b_64)\
	    .addTextPara("convert_to_jpg","1")\
	    .post()
	print ('json_res data is:', json_res)
	data =json.loads(json_res)
	print data
	jsonobj = json.loads(data)
	print jsonobj['showapi_res_body']['Result']
    #datalist.txt中存入了[图片名,识别到的验证码值]
	fw = open('data_list.txt','a')
	fw.write(str(png_name)+',')
	fw.write(str(jsonobj['showapi_res_body']['Result'])+'\n')
	fw.close()
```
经过对于api的请求我们可以得到一份验证码的字典文件。通过此文件可以通过key=验证码的文件id，得到验证码的真实值。

![验证码字典表文件](http://upload-images.jianshu.io/upload_images/1779926-17b7f634ee72714f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
此时我们似乎已经大功告成解决了验证码这一难题。
>可是有一个悲伤的消息：云大urp的验证码过于复杂，api的识别率有限，我们还需要进一步提升该字典表的精度。下一篇我们介绍如何使用GUI小程序来进一步提高它的精准度，满足我们的要求。
