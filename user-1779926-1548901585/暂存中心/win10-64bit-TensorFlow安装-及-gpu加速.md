1. win10 家庭版64位

![mark](http://myphoto.mtianyan.cn/blog/180208/LLfbIlLidi.png?imageslim)

2. cuda驱动:

![mark](http://myphoto.mtianyan.cn/blog/180208/C0GcE99a8J.png?imageslim)

3. cudnn加速包:

![mark](http://myphoto.mtianyan.cn/blog/180208/FJ2Djdkl0I.png?imageslim)

cuda安装全量模式

![mark](http://myphoto.mtianyan.cn/blog/180208/lm9Ch7AJB1.png?imageslim)

验证cuda安装成功。

![mark](http://myphoto.mtianyan.cn/blog/180208/DABc3Jdc2I.png?imageslim)

``` 
pip install tensorflow_gpu-1.0.1-cp35-cp35m-win_amd64.whl
```
bin目录加入C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0\include

2. 下载完成后解压缩。里面有bin、include、lib三个目录，将三个文件夹复制到安装CUDA的地方覆盖对应文件夹，默认文件夹在：
C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0

注意：一定要先装vs再装

C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0\bin 加入bin目录
