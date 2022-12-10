### PVE宿主机下制作openwrt的LXC模板

#### 第一步：

上传`*.img.gz`固件至root目录

```
gzip -d *.img.gz
```

#### 第二步：

1、如果是`rootfs.img`固件需要按照下面的方式进行解压。

```
apt install squashfs-tools #安装解压包：
unsquashfs 固件路径.img #解压
```

解压完成后你在同级目录下会得到`squashfs-root`文件夹，然后进入该文件夹，跳至3步第三步进行打包

2、其它固件则需要通过挂载镜像，得到内部文件：

```
mkdir op
root_partition=$((`fdisk -l /root/*.img | grep .img2 | awk '{print $2}'` * 512))
mount -o loop,offset=$root_partition /root/*.img /root/op
```

#### 第三步：

```
cd op && tar zcf /var/lib/vz/template/cache/*.tar.gz * &&cd ..
umount /root/op && rm -rf *.img
```

