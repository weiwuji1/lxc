#### 第一步：

上传`固件名称.img.gz`固件至root目录后gzip解压

```
gzip -d 固件名称.img.gz
```

#### 第二步：

1、如果是`rootfs.img`固件需要按照下面的方式进行解压

```
apt install squashfs-tools #安装解压包：
unsquashfs 固件名称.img #解压img固件
```

解压完成后在同级目录下会得到`squashfs-root`文件夹，然后进入该文件夹，进行打包

```
cd squashfs-root && tar zcf /var/lib/vz/template/cache/固件名称.tar.gz * &&cd ..  # 打包至PVE中LXC模板路径
rm -rf 固件名称.img
```

2、其它固件则需要通过挂载镜像，得到内部文件：

```
mkdir op
root_partition=$((`fdisk -l /root/固件名称.img | grep .img2 | awk '{print $2}'` * 512))
mount -o loop,offset=$root_partition /root/固件名称.img /root/op
```

打包固件，弹出挂载，删除`.img`固件
```
cd op && tar zcf /var/lib/vz/template/cache/固件名称.tar.gz * &&cd ..  # 打包至PVE中LXC模板路径
umount /root/op && rm -rf 固件名称.img
```

#### 其它：

如果要在lxc容器中使用tun/tap设备，需要在配置文件中添加：

```
lxc.mount.entry: /dev/net/tun dev/net/tun none bind,create=file
lxc.cgroup.devices.allow: c 10:200 rwm
```

配置文件路径为：`/etc/pve/lxc/虚拟机编号.conf`
