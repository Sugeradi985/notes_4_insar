今天修改save_qgis.py想要实现逐像元垂直向计算并输出shapefile文件，

在windows 10 系统修改好以后，通过共享文件夹拷贝到虚拟机的mintpy文件夹，运行时报错：

```shell
/usr/bin/env: ‘python3\r’: No such file or directory 
```
百度了一下，这个报错是因为**文件格式**的问题，

起初这个文件是在windows下写的, 

**第一行指定了python解释器路径后,**

放到Linux下执行就会报这个错,

所以需要修改它的文件格式。

#### 1.异常原因：

DOS系统下和Linux系统下对于**换行键**的表示不同。

在windows下，用连续的`'\r'`和`'\n'`两个字符进行换行。`'\r'`为回车符，`'\n'`为换行符。

```shell
#!/usr/bin/env python\r\n
```

在Linux下，用`'\n'`进行换行。

```shell
#!/usr/bin/env python\n
```

所以windows下的程序会认为`#!/usr/bin/env python`是一行，而`linux会认为#!/usr/bin/env python\r`是一行。

#### 2.解决方法：

看了很多网上的教程，说用VIM编辑器进行修改，由于我不是很常用VIM编辑器，

发现可以用dos2unix这个包进行转换。

首先安装dos2unix的包，然后通过dos2unix这个命令即可完成转换。

```shell
sudo apt-get install dos2unix
dos2unix <filename>
```
