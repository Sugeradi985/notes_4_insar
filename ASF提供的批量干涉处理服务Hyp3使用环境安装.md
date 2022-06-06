**HyP3**是用于处理合成孔径雷达（SAR）图像的自动化服务。

目前可以支持免费处理的功能为生产Sentinel-1 RTC产品、Sentinel-1 InSAR产品、Sentinel-1 AutoRIFT产品。

Sentinel-1 RTC产品即辐射地形校正产品（GAMMA软件生成），产品为地理编码后UTM投影的GeoTIFF格式，像素间距30m。

Sentinel-1 InSAR产品即干涉处理产品，由GAMMA软件进行SAR数据干涉生成干涉图，经过解缠、地理编码UTM投影，GeoTIFF格式。

Sentinel-1 AutoRIFT产品为NASA MEaSUREs ITS_LIVE项目的一部分开发的功能跟踪算法处理产品，可用于生成速度图。

### 以下为ASF官网对HyP3的介绍：

https://hyp3-docs.asf.alaska.edu/

# ASF HyP3

***Alaska Satellite Facility's Hybrid Pluggable Processing Pipeline\***

HyP3 is a service for processing Synthetic Aperture Radar (SAR) imagery that addresses many common issues for users of SAR data:

- Most SAR datasets require at least some processing to remove distortions before they are analysis-ready
- SAR processing is computing-intensive
- Software for SAR processing is complicated to use and/or prohibitively expensive
- Producing analysis-ready SAR data has a steep learning curve that acts as a barrier to entry

HyP3 solves these problems by providing a free service where people can request SAR processing on-demand. These processing requests are picked up by automated systems, which handle the complexity of SAR processing on behalf of the user. HyP3 doesn't require users to have a lot of knowledge of SAR processing before getting started; users only need to submit the input data and set a few optional parameters if desired. With HyP3, analysis-ready products are just a few clicks away.

-------------------------------------

**注意：官网已经有比较详细的安装和使用说明，现进行简单梳理，大家可以按照下面步骤进行hyp3环境部署**

### HyP3环境配置

1. 利用conda创建虚拟环境

```shell
conda create -n hyp3 python=3.9
```

2. 激活虚拟环境，并安装hyp3-sdk

```shell
conda activate hyp3
```

```shell
conda install -c conda-forge hyp3_sdk
```

或者用pip安装：

```shell
python -m pip install hyp3_sdk
```

3. 安装[asf_search](https://docs.asf.alaska.edu/asf_search/basics/)库，用于检索数据

```shell
conda install -c conda-forge asf_search
```

至此，HyP3环境部署完成，可以利用代码进行数据检索、job的提交和下载等操作了。

**如果需要对干涉图进行时序反演和形变速率估算，建议安装[Mintpy](https://github.com/insarlab/MintPy/)软件包，可以参考另外一篇教程：[InSAR处理环境安装](https://github.com/Sugeradi985/notes_4_insar/blob/main/InSAR处理环境安装——ISCE%2BMintpy.md)。**

### 干涉图任务提交与后续处理

1. 利用HyP3提交干涉图job

检索数据及提交干涉任务可以参考：[Select InSAR pairs with ASF Search](https://nbviewer.org/github/ASFHyP3/hyp3-docs/blob/main/docs/tutorials/hyp3_insar_stack_for_ts_analysis.ipynb#1.-Select-InSAR-pairs-with-ASF-Search)

需要注意的是，为了进行时序处理，提交干涉处理任务时，**请至少将一对干涉图增加DEM数据选项**，这样下载的产品会包含DEM数据。

2. 利用Mintpy进行时序处理前的准备工作

##### 公共区裁剪

在使用Mintpy进行时序处理前，需要对干涉图进行裁剪，**保证所有干涉图尺寸和覆盖范围一致**，并且需要按照一定**路径规则**进行存放干涉图。

对干涉图和DEM等数据进行公共区裁剪：[Subset all GeoTIFFs to their common overlap](https://nbviewer.org/github/ASFHyP3/hyp3-docs/blob/main/docs/tutorials/hyp3_insar_stack_for_ts_analysis.ipynb#3.1-Subset-all-GeoTIFFs-to-their-common-overlap)

或者使用hyp3lib/[cutGeotiffs.py](https://github.com/ASFHyP3/hyp3-lib/blob/develop/hyp3lib/cutGeotiffs.py)

##### 数据存放路径

干涉图存放路径如下所示：[Example data directory for Hyp3](https://mintpy.readthedocs.io/en/latest/dir_structure/#asf_hyp3)

```shell
$DATA_DIR/RidgecrestSenDT71
├── hyp3
│   ├── S1AA_20190622T135157_20190704T135158_VVP012_INT80_G_ueF_4C43
│   │   ├── S1AA_20190622T135157_20190704T135158_VVP012_INT80_G_ueF_4C43_corr_clip.tif
│   │   ├── S1AA_20190622T135157_20190704T135158_VVP012_INT80_G_ueF_4C43_dem_clip.tif
│   │   ├── S1AA_20190622T135157_20190704T135158_VVP012_INT80_G_ueF_4C43_lv_theta_clip.tif
│   │   ├── S1AA_20190622T135157_20190704T135158_VVP012_INT80_G_ueF_4C43_unw_phase_clip.tif
│   │   ├── S1AA_20190622T135157_20190704T135158_VVP012_INT80_G_ueF_4C43_water_mask_clip.tif
│   │   ├── S1AA_20190622T135157_20190704T135158_VVP012_INT80_G_ueF_4C43.txt
│   │   └── ...
│   ├── S1AA_20190622T135157_20190716T135159_VVP024_INT80_G_ueF_BA28
│   │   ├── S1AA_20190622T135157_20190716T135159_VVP024_INT80_G_ueF_BA28_corr_clip.tif
│   │   ├── S1AA_20190622T135157_20190716T135159_VVP024_INT80_G_ueF_BA28_unw_phase_clip.tif
│   │   ├── S1AA_20190622T135157_20190716T135159_VVP024_INT80_G_ueF_BA28.txt
│   │   └── ...
│   └── ...
└── mintpy
    └── RidgecrestSenDT71.txt
```

##### 在smallbaseline.cfg中的`load_data`部分修改相应参数：

```shell
mintpy.load.processor        = hyp3
##---------interferogram datasets:
mintpy.load.unwFile          = $DATA_DIR/RidgecrestSenDT71/hyp3/*/*unw_phase_clip.tif
mintpy.load.corFile          = $DATA_DIR/RidgecrestSenDT71/hyp3/*/*corr_clip.tif
##---------geometry datasets:
mintpy.load.demFile          = $DATA_DIR/RidgecrestSenDT71/hyp3/*/*dem_clip.tif
mintpy.load.incAngleFile     = $DATA_DIR/RidgecrestSenDT71/hyp3/*/*lv_theta_clip.tif
mintpy.load.waterMaskFile    = $DATA_DIR/RidgecrestSenDT71/hyp3/*/*water_mask_clip.tif
```

然后便可以使用Mintpy进行时间序列反演和形变速率的估算了。
