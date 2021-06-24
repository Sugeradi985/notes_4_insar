## 下载DEM文件并修改xml（重要！） -s后面是分辨率，1代表1弧度，3为3弧度
```shell
dem.py -a stitch -b 18 20 100 102 -r -s 1 -c
```
## 修改xml文件，否则后面无法读取！
```shell
fixImageXml.py -f -i demLat_*.dem.wgs84
```

## SLC文件不需要解压缩，link到SLC

## 修改默认输出图片的size

In Display.py there is the actual conversion from ppm to png (with a resize option). You can change this value and use it. However, the generated PNG file can be large and wont load easily.
