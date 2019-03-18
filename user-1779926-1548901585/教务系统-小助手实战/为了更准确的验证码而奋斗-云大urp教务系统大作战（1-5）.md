在上一节验证码爬取并识别中我们通过阿里云市场的api识别了验证码，不过验证码的正确率经测试只有60%左右`登录了10次，失败了4次`
1. 作为一个稳重的程序员，怎么能像天气预报一样不靠谱呢。因此我又写了一段程序，通过对于服务器不停的登录请求，来验证我们的验证码值是否正确，并把错误的存入一个新的error.txt文件。
2. 对于error.txt的内容我又通过一个简易的gui小程序进行人工的肉眼暴力识别。

**产生错误列表文件的代码：**
```
import json
import requests
from requests import exceptions
#此处打开的文件为上一节识别的验证码字典表
fr = open('dict.txt','r')
dic = dict()
for line in fr:
    line = line.strip().split(',')
    dic[line[0]] = line[1]
# print(dic)
fr.close()
URL = 'http://202.203.209.96/v5api/api/GetLoginCaptchaInfo/f525199d-9d7b-4547-9094-1528bed37496'
headers={'User-Agent':'Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13B143 Safari/601.1'}
##请求当然要暴力一点，先来他个100万
for x in xrange(1,1000000):
	response = requests.get(URL,headers=headers)
	imgguid  = response.json()['ImgGuid']
	caputre  = dic[imgguid]
	tempguid = response.json()['TempGuid']
	url2 = 'http://202.203.209.96/v5api/OAuth/Token'
	response2 = requests.post(url2,headers=headers,data={'grant_type':'password','username':'20141120273','password':'tudoudou5283|%s*%s'%(caputre,tempguid),'client_id':'ynumisSite'})
	# print response2.request.headers
	print response2.status_code
	errorcode =set()
	if(response2.status_code !=200):
		errorcode.add(imgguid)
		fw = open('error.txt','a')
		fw.write(str(imgguid)+'\n')
		fw.close()
	jsonobj = json.loads(response2.text)
```
上面代码中涉及到的一点模拟登陆验证的代码请参照模拟登陆小节。
>此时我们已经拥有了一个error.txt
我们下一步通过gui来对于该error内容进行识别。

GUi代码：
```
#encoding: utf-8 
from Tkinter import *
list=[]
with open('error.txt') as f:
    for line in f.readlines():
        list.append('.'.join([line[:-1],'gif']))

list2={}
root=Tk()
root.geometry('200x120')

filename=list.pop()
photo=PhotoImage(file=filename)
img=Label(root,image=photo)
img.pack()

def get_code(event,img):
    global photo
    global filename
    global list2
    list2[filename]=code_text.get()
    code_text.set("")
    with open("right.txt",'a') as m:
        m.write(filename[:-4]+','+list2[filename]+"\n")
    filename=list.pop()
    photo=PhotoImage(file=filename)
    img["image"]=photo
    print list2
    print filename
    
def get_code_Adaptor(fun,**kwds):
    return lambda event,fun=fun,kwds=kwds: fun(event, **kwds)
    
code_text=StringVar()
code=Entry(root,textvariable=code_text)
code.focus_set()
code.bind('<Key-Return>',get_code_Adaptor(get_code,img=img))
code.pack()

button=Button(root,text='Press Enter',command=get_code_Adaptor(get_code,img=img))
button.pack()


root.mainloop()


```

![验证码gui程序](http://upload-images.jianshu.io/upload_images/1779926-8e91623cddf3c6e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
