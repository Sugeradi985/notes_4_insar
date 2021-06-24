# StaMPS使用笔记

1. ALOS PALSAR L1.0 raw data需要用ROI_PAC读取生成SLC文件才能进入下一步。

   ROI_PAC本身不支持读取PALSAR数据，需要下载ALOS_PRE_PROCESS那个代码来预处理L1.0数据，再开始ROI_PAC的读取和处理。(make_raw_alos.pl)但该代码不能读取L1.1数据。L1.1数据是after focusing的，不需要用ROI_PAC读取，可以进入下一步直接用DORIS读取。

2. 用cygwin跑DORIS的时候，出现不能顺利编译代码的问题。原因是cygwin上自动安装的gcc新版本有问题，需要重新打开cygwin的安装文件，选择旧版本的gcc。(所以一定要留着cygwin那个安装文件啊!)另外最后，结果证实windows+cygwin的办法还是不行啊，调用matlab搞不定orz 所以就该从一开始就用linux啊。。。

3. make_orbits阶段生成make_ifgs.list这步在电脑A成功，在电脑B则只生成空白文件，看了一下似乎有的情况下不能自动生成，这时候就得手动填一下各个slave文件夹的路径，再往下跑。

4. make_coreg这步似乎已经用到了matlab，所以在没有matlab的情况下，这步跑不下去，可以用make_coreg_simple。但是之后也还得用到matlab。

5. step_master_timing这步之前需要编辑timing.dorisin里的DEM文件信息。DEM需要是WGS84坐标投影。可以通过DORIS附带的construct_dem.sh代码自动下载拼接生成需要的DEM文件。不过DORIS里附带的这段代码里的数据下载地址已不能用，需要到网上去下载一个修改过的construct_dem.sh文件。
使用construct_dem.sh的前提是要安装GMP，GMP安装的默认地址比较不同，在/usr/lib/gmt/bin，别忘了在跑代码之前先把GMP的文件路径加入PATH。但是即使export声明了，我这儿还是无法顺利在跑construct_dem.sh的时候调用GMP命令，所以最后干脆打开construct_dem.sh文件，把里面需要调用GMP命令的地方全都手动改成了绝对路径，问题终于解决。
按照群里的说明，用ENVI生成的ENVI Standard格式DEM文件应该是可以直接用的，不过我这儿试了一下目前还不认，继续摸索。

6. 剪裁步骤，最开始图省事用了master_crop,in和step_master_read。结果发现通过指定像素范围的方法剪裁，因为图跟图之间错位太多，在之后的coregistration和resampling的步骤会出问题，错位太多无法匹配之类的。现在想想这步大概还是用master_crop_geo.in和step_master_read_geo来剪裁比较好。。。。

7. DEM必须是纯二进制文件，不包含头文件，GAMMA支持的是4字节浮点型和2字节短整型。字节顺序必须是大字节在前。如果DEM是从windows下软件如erdas获得的，字节顺序是小字节，需要交换字节。使用swap_bytes命令。

8. Envi拼接的DEM没有去掉重叠行，所以会多出一行一列，用Erdas就没有这个问题。DataPre下的mosaic命令进行拼接。然后通过Import/Export模块将tif格式输出为通用二进制模式Generic Binary，输出为BIL，Signed 16 Bit，Byte Order 为Motorola（Big Endian）。

## 调用doris下载拼接dem

```shell
construct_dem.sh name W E S N STRM1/3
```
## 调用doris画图

```shell
plotcpm CPM_Data.*
```
Google earth安装

