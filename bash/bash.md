# 记录一些用的上的bash

## 测试磁盘写速度
``` shell
dd if=/dev/zero of=/tmp/output.img bs=8k count=256k conv=fdatasync; rm -rf /tmp/output.img
```

```
shesuyo 
262144+0 records in
262144+0 records out
2147483648 bytes (2.1 GB) copied, 51.5157 s, 41.7 MB/s
```
```
local
262144+0 records in
262144+0 records out
2147483648 bytes (2.1 GB) copied, 30.1346 s, 71.3 MB/s
```
```
阿里云
262144+0 records in
262144+0 records out
2147483648 bytes (2.1 GB) copied, 26.8082 s, 80.1 MB/s
```

## 磁盘读速度
```
hdparm -Tt /dev/sda
```
```
local
/dev/sda:
 Timing cached reads:   33164 MB in  2.00 seconds = 16612.77 MB/sec
 Timing buffered disk reads: 316 MB in  3.00 seconds = 105.24 MB/sec
```
阿里云不知道为什么没有 `/dev/sda`，于是使用`fdisk -l` 来查看
```
Disk /dev/vda: 536.9 GB, 536870912000 bytes, 1048576000 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x0008d207

   Device Boot      Start         End      Blocks   Id  System
/dev/vda1   *        2048  1048573951   524285952   83  Linux
```
然后我就使用/vda来代替/sda了
```
/dev/vda:
 Timing cached reads:   16728 MB in  2.00 seconds = 8376.26 MB/sec
 Timing buffered disk reads: 242 MB in  3.03 seconds =  79.97 MB/sec
```

## 进程挂起 && 进程移到前台 && 将进程移动到后台

``` shell
[root@lan17 app]# ./app 
2017/12/18 15:46:59 app run
^Z
[1]+  Stopped                 ./app
[root@lan17 app]# bg
[1]+ ./app &
[root@lan17 app]# fg
./app
```

## unzip 到目录
```
unzip package_name.zip -d dir_namentpdate 
```