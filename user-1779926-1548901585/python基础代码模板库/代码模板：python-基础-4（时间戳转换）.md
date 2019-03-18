## 4  时间
 时间戳指的是从1970年1月1日0时0分0秒开始，到某一时刻所经历的秒数，可以是整数或者小数，后者的精度更高
python使用时间戳转换前提：`import time`
 ```python
import time
a = int(time.time()) #获取当前时间
print a,type(a)  # 从1970年1月1号到当前时间的秒数
```
### 4.1 时间戳转时间文本
`%Y-%m-%d` 等都是时间字段，前者表示四位的年份，后者表示两位的月份
```python
a = 1489580027
a = time.strftime("%Y-%m-%d %H:%M:%S", time.localtime(a))
#2017-03-15 20:13:47
a = time.strftime("%Y-%m-%d", time.localtime(a))
#2017-03-15
a = time.strftime("%H:%M:%S", time.localtime(a))
#20:13:47
print a
```
### 4.2 时间文本转时间戳，精确到秒
```python
a = '2017-03-15 20:13:47'
timeStamp = int(time.mktime(time.strptime(a, "%Y-%m-%d %H:%M:%S")))
#a与字符串格式匹配就行。省略的部分会默认补全。
#时间会默认为00:00:00但是年份不能省略
print timeStamp
```
