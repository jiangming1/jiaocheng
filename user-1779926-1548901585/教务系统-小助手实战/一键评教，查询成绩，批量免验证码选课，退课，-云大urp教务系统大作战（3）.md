这一小节没有什么好讲的，如果你学会了上一小节的中心思想：
**从真实世界看程序世界**
那么我们可以触类旁通的继续通过f12控制台对于真实的查询成绩，加课，退课，教学评估的流程进行模拟。
此处注意几个点：
1.  注意方法的输入与输出
2. 批量加课通过一个list的for循环实现
3. 一键教学评估通过for循环实现。

###the method to get grade（查询成绩）
```
#传入token值返回成绩的json
def getGrade(token):
	URL = 'http://202.203.209.96/v5api/api/Result/'
	headers={'User-Agent':
	'Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13B143 Safari/601.1',
	'Authorization':'Bearer %s'%token}
	response = requests.get(URL,headers=headers)
	# print response.request.headers
	jsonobj = json.loads(response.text)
	print jsonobj['TeachClassList']
```
###免验证码加课：免验证码实现及字典表请查看前几节课程
```
#the method to add class
def addClass(token,classCode_add):
	URL = 'http://202.203.209.96/v5api/api/xk/addDirect'
	URL2 = 'http://202.203.209.96/v5api/api/xk/Captcha'
	headers={'User-Agent':
	'Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13B143 Safari/601.1',
	'Authorization':'Bearer %s'%token}
	fr = open('../data/dict.txt','r')
	dic = dict()
	for line in fr:
	    line = line.strip().split(',')
	    dic[line[0]] = line[1]
	fr.close()
	response = requests.get(URL2,headers=headers,json={'ImgGuid':'68f08ba6-8262-4726-a4ea-b1a7b346b50e'})
	print response
	imgguid  = response.text[1:-1]
	print imgguid
	caputre  = dic[imgguid]
	response = requests.post(URL,headers=headers,json={'id':'%s'%classCode_add,'captcha':'%s'%caputre})
	# print response.request.body
	print response.status_code
	print response.text
````
###退课remove class
```
def removeClass(token,classCode_remove):
	URL = 'http://202.203.209.96/v5api/api/xk/remove'
	headers={'User-Agent':
	'Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13B143 Safari/601.1',
	'Authorization':'Bearer %s'%token}
	response = requests.post(URL,headers=headers,json={'id':'%s'%classCode_remove})
	print response.status_code
	print response.text
```
###教学评估Teaching evaluation
```
def teachEval(token):
	URL = 'http://202.203.209.96/v5api/api/TeachEvaluation'
	headers={'User-Agent':
	'Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13B143 Safari/601.1',
	'Authorization':'Bearer %s'%token}
	response = requests.get(URL,headers=headers)
	jsonobj =json.loads(response.text)
	classcode =jsonobj['TeachClassList']
	for x in xrange(0,len(classcode)):
		classcode =jsonobj['TeachClassList'][x]['TeachClassId']
		response = requests.post(URL,headers=headers,json={
		"TeachClassId":"%s"%classcode,
		"Score":99,
		"SurveyAnswerStr":"22111111111111",
		"Comment":"教学内容丰富有效，很受同学欢迎。%s"%x})
		print response.text
```

此处代码有些过于重复但是为了后期每个方法的独立性而进行了冗余。后期还有改进的空间。
