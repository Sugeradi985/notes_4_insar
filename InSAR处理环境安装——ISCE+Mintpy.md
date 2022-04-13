### 声明

知乎收到许多留言询问关于insar处理的环境安装的问题，今天抽出时间写一个教程。

需要mark的是：本教程用到的软件、素材来源于网络，我就是个搬运工。

在此感谢各位大佬的奉献！

### 正文部分：

#### 注意：处理环境为Linux或者发行版系统，推荐使用Ubuntu 20.04

#### 以下步骤默认已经安装好了Ubuntu 20.04系统

#### 1.安装conda

conda环境的话，这里推荐大家安装**miniconda**，更轻量；

（当然也可以安装anaconda，如果要自己写python代码或者调试会更方便）

国内还是推荐到**清华大学开源软件镜像站**下载安装包：

这里跳转：https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/

![image](https://user-images.githubusercontent.com/83938492/163108251-6b28ee37-7dcd-4db0-a10b-4846c1bb521c.png)

版本的话**选择最新**的就ok；

比如此时最新的版本为：**Miniconda3-py39_4.11.0-Linux-x86_64.sh**

![image](https://user-images.githubusercontent.com/83938492/163108584-04d8f39e-3994-4634-b709-6cf4c1350b20.png)

下载完成后，按照如下步骤安装：

##### （1）在home文件夹下创建tools文件夹，用以存放insar处理相关的软件和库包：

打开终端（即Terminal，我习惯叫命令行）

**（本文中，以下表示方式为命令行输入：）**

```shell
mkdir -p ~/tools; cd ~/tools
```

下载的Miniconda3-py**-Linux-x86_64.sh文件默认保存在Downloads文件夹中；

##### （2）通过以下命令安装：

```shell
bash ~/Downloads/Miniconda3-py**-Linux-x86_64.sh -b -p ~/tools/miniconda3
~/tools/miniconda3/bin/conda init bash
```

##### （3）关闭终端，然后重新打开一个新的终端；**（这一步是为了让方才的设置生效）**

##### （4）在终端中输入以下命令：

```shell
conda config --add channels conda-forge
conda config --set channel_priority strict
conda install wget git tree mamba --yes
```

上面最后一行命令是安装了几个工具，wget、git、tree和mamba
后面安装会用到这些工具。

#### 2.安装ISCE2、MintPy

如今，isce2和mintpy已经包含在conda源里面，可以直接通过conda或者mamba安装；

因此有两个方式可以安装：

一是直接通过conda源安装isce2和mintpy，是release版本，更稳定；

二是通过github发布的源码安装，是development版本，更新；

##### 第一种方式，conda安装release版本：（本人没有用过这种方式，仅供参考！！！）

```shell
mamba install isce2 mintpy
```

一个命令行搞定！

**后续需要配置环境变量之类的可以参考第二种方式的（4）中的配置文件**

##### 第二种方式，是从源码安装，步骤多一些，但是不麻烦，就是粘粘粘~

##### 这里搬运Yunjunz贡献的安装文件conda_envs（ https://github.com/yunjunz/conda_envs ）

不熟悉linux系统的同学，不用再动手配置了~

##### （1）从github下载源码

```shell
cd ~/tools
mkdir isce2; cd isce2
mkdir build install src; cd src
git clone https://github.com/isce-framework/isce2.git

cd ~/tools
git clone https://github.com/aria-tools/ARIA-tools.git
git clone https://github.com/insarlab/MintPy.git
git clone https://github.com/insarlab/PySolid.git
git clone https://github.com/insarlab/PyAPS.git
git clone https://github.com/yunjunz/conda_envs.git
```
--------
注意：

github服务器在国外，如果下载太慢或者失败，建议逐一手动下载；

复制.git前面的地址，

比如复制并粘贴 https://github.com/isce-framework/isce2 到浏览器地址栏，可以打开isce2源码文件；

点击绿色的Code按钮——Download，

下载完了是一个.zip压缩包,

把它们逐一解压缩到~/tools文件夹；

----------

##### （2）创建虚拟环境

准备好了所有的源码文件后，进入到环境的配置步骤了；

利用conda创建一个虚拟环境，比如‘insar’：

```shell
conda create -name insar python=3.9 --yes
conda activate insar
```

**注意：这里需要设置一下python的版本，python3.10已经发布了，但是经过测试部分脚本运行会出错，因此我们用python3.9**

##### （3）配置依赖库并安装isce和mintpy等

首先是isce2：

```shell
mamba install -y --file conda_envs/insar/requirements.txt --file MintPy/docs/requirements.txt --file ARIA-tools/requirements.txt isce2
```

如果此处报错，请检查并核对上面命令行中文件的存放路径是否正确~

然后是mintpy等：

```shell
python -m pip install -e MintPy
python -m pip install -e PySolid
python -m pip install -e PyAPS
```

最后是用pip安装conda源中没有的依赖项

```shell
ln -s ${CONDA_PREFIX}/bin/cython ${CONDA_PREFIX}/bin/cython3
python -m pip install scalene
python -m pip install ipynb
```

##### （4）配置环境变量

这一步直接使用yunjunz提供的配置文件就可以了，非常方便；

只需要在环境变量的配置文件，如~/.bashrc文件中,

创建一个alias来方便激活环境和配置文件：

```shell
alias load_insar='conda activate insar; source ~/tools/conda_envs/insar/config.rc'
```

所以，这里可以根据自己需要灵活配置了；

比如我本人为了方便切换alos和sentinel以及tsx等，

在config.rc中也添加了alias来方便切换。

至此，isce+mintpy的insar处理环境就安装并配置好了。

可以测试一下成功与否。

##### （5）测试安装

打开一个终端（Ctrl+Alt+T），输入：

```shell
load_insar
```

激活环境，并在终端中逐一输入以下命令：

```shell
topsApp.py -h            # 测试ISCE-2
smallbaselineApp.py -h   # 测试MintPy
```

##### 如果看到提示信息，说明安装成功了！

**如果遇到问题，建议参看yunjunz的教程： https://github.com/yunjunz/conda_envs

**最后，感谢所有让insar开源处理软件安装和使用更容易的奉献者！

**如有侵权，即为致歉，并请联系删除**
