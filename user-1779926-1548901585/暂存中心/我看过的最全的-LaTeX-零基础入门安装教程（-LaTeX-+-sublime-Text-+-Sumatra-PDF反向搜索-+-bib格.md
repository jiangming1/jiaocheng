
 LaTeX + sublime Text + Sumatra PDF反向搜索 + bib格式文件文献引用（及国标化配置）
找了一大堆教程没有一个把细节都一篇文章讲完的。于是自己写一篇

1. 安装TEx live 2017和Sumatra PDF：
地址：http://mirrors.ustc.edu.cn/CTAN/systems/texlive/Images/
- 下载最新全量ios：
![textlive镜像](http://upload-images.jianshu.io/upload_images/1779926-e4d516ceb5bc5e88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 右键使用资源管理器打开该ios，双击 install=tl-advanced.bat文件
出现如下图安装界面。点击最下面中间位置安装。等待很久。
![安装界面](http://upload-images.jianshu.io/upload_images/1779926-ec99282e75ded0ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###安装Sumatra PDF

地址：https://www.sumatrapdfreader.org/download-free-pdf-viewer.html

![下载](http://upload-images.jianshu.io/upload_images/1779926-7085499aa2696e83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下载好之后全部默认下一步。

2. 安装sublime Text 3 。然后自行配置好Package Control：
https://packagecontrol.io/installation

- `Ctrl+Shift+P`，输入 `install`
- 输入`LatexTools` 右键回车安装。

配置LatexTools：

如下图选择：
![选择](http://upload-images.jianshu.io/upload_images/1779926-0fb5bf93d6b7fec0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![将windows部分修改如上](http://upload-images.jianshu.io/upload_images/1779926-4fa3a716f8fc47c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

路径自行根据个人安装位置配置。

![build模式](http://upload-images.jianshu.io/upload_images/1779926-c4d90c42223ca8fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####Sumatra PDF选项中没有设置反向搜索命令行解决方案。

- 先把安装路径加入系统环境变量path中。
- 使用sublime text 新建一个test.tex文件

```
    %!TEX program = xelatex
    % 使用 ctexart 文类，UTF-8 编码
    \documentclass[UTF8]{ctexart}
    \begin{document}
    This is the context of the article.\cite
    这就是文章的所有内容。
\end{document}  
```

然后使用crtl+b运行之后会自动弹出pdf。此时Sumatra PDF的选项里会有设置反向搜索命令行。
![反向搜索设置](http://upload-images.jianshu.io/upload_images/1779926-71a04fc4fdae08b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
"C:\Sublime Text 3\sublime_text.exe"  "%f:%l"
```

你可以试着双击pdf会回到对应的sublime 中的文字。

###使用bib格式文件编译表头。
新建test.tex
```
      %!TEX program = xelatex
    % 使用 ctexart 文类，UTF-8 编码
    \documentclass[UTF8]{ctexart}
    \begin{document}
    This is the context of the article.\cite{radford2015unsupervised}
    这就是文章的所有内容。
\bibliographystyle{plain}%
\bibliography{hi}
\end{document}  
```
新建hi.bib文件。

```
@article{radford2015unsupervised,
  title={Unsupervised representation learning with deep convolutional generative adversarial networks},
  author={Radford, Alec and Metz, Luke and Chintala, Soumith},
  journal={arXiv preprint arXiv:1511.06434},
  year={2015}
}
```
```
\cite{radford2015unsupervised}
\bibliographystyle{plain}%
\bibliography{hi}
```
1.  上面三行中第一行是引用bib文件名。如上（radford2015unsupervised）
2. 第二行是选取bib样式。
3. 第三行是选取bib文件名。（注意bib文件和tex文件在同一目录）
默认安装自带八种配置。
- plain，按字母的顺序排列，比较次序为作者、年度和标题.
- unsrt，样式同plain，只是按照引用的先后排序.
- alpha，用作者名首字母+年份后两位作标号，以字母顺序排序.
- abbrv，类似plain，将月份全拼改为缩写，更显紧凑.
- ieeetr，国际电气电子工程师协会期刊样式.
- acm，美国计算机学会期刊样式.
- siam，美国工业和应用数学学会期刊样式.
- apalike，美国心理学学会期刊样式.

![生成的pdf](http://upload-images.jianshu.io/upload_images/1779926-68fbcd3cc70c75f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####配置bibliographystyle为国标样式（GB）

网址：https://raw.githubusercontent.com/Haixing-Hu/GBT7714-2005-BibTeX-Style/master/gbt7714-2005.bst

另存到本地为gbt7714-2005.bst

将bst文件放进`C:\texlive\texmf-local\bibtex\bst`

![路径](http://upload-images.jianshu.io/upload_images/1779926-a641b76da2c1f1e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
\bibliographystyle{gbt7714-2005}%
```
将上面的test.tex中文件更改如上一行：
然后将上次产生的各个文件都删除。只保留test.tex和hi.bib
ctrl+b运行：

![国标参考文献引用](http://upload-images.jianshu.io/upload_images/1779926-3e987e9f499becf4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
















 
