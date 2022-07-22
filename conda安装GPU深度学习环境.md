### 背景
在进行深度学习练习时候，经常需要copy别人的代码进行练手，github有非常丰富的学习资源，也有个性化的代码分享。

但是不同代码开发环境差一笔较大，比如python版本、pytorch版本、cuda、cudnn等，需要互相配合，稍有不慎，便无法顺利运行代码。

每次重新搭建环境，费时费力，而且很多库包都在外网服务器，下载慢的要死

主要是，如果后面想要用之前的环境，就还要费劲，手动切换或者干脆推倒重建，太折腾了

后来用了conda后，就寻思能不能通过conda管理深度学习环境呢？

主要就是cuda、cudnn这些环境的管理，python和pytorch之前就可以通过conda切换环境来管理了

在万能的百度查了一番后，总结出一套深度学习环境的流程，简洁都是干货~

### 步骤（默认装好了conda、python、显卡驱动这些。。。）

#### 1、查找可以安装的cuda

1.1 激活conda环境

1.2 执行下面命令检索可用cuda版本

```shell
conda search cudatoolkit --info
```

会返回源内所有的cuda版本信息，并附有url

1.3 可以挑选合适版本的cuda，复制链接下载，或者用wget下载

1.4 安装本地已下载好的cuda

```shell
conda install --use-local /cuda/directory/
```

#### 2、查找可以安装的cudnn版本

2.1 执行下面命令检索可用的cudnn版本，这里需要注意**一定要和刚安装的cuda版本相对应**

```shell
conda search cudnn --info
```

2.2 下载cudnn，url在浏览器下载或者wget下载

2.3 安装本地cudnn

```shell
conda install --use-local /cudnn/directory/
```

#### 3、测试（需要pytorch）

conda环境安装的cuda和cudnn不能使用nvcc -V命令测试，我们可以用pytorch来测试

3.1 安装与cuda相匹配的pytorch包，推荐[官网安装](https://pytorch.org/get-started/locally/)

```shell
conda install pytorch torchvision torchaudio cudatoolkit=11.3 -c pytorch
```

3.2 查询cuda版本

```shell
python
```

```python
# python 环境下
import torch
print(torch.version.cuda)
```

3.3 查询cudnn版本

```shell
python
```

```python
# python 环境下
import torch
print(torch.backends.cudnn.version())
```
