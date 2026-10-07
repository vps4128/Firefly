---
title: Debian使用sshfs将StorageBox挂载到本地
published: 2026-10-06
pinned: false
description: 将StorageBox挂载到本地，方便进行管理
tags: [Debian,Linux]
category: Linux
---


1.debian安装sshfs
=============
```
sudo apt update -y
sudo apt install sshfs -y
```
2.确认是否安装成功
=============
```
sshfs --version
```
返回如下图显示版本号即安装成功
```
SSHFS version 3.7.3
```
3.创建本地挂载目录
=============
```
sudo mkdir -p /mnt/storage
```
4.创建密码文件
=============
```
mkdir -p /root/.config/sshfs
nano /root/.config/sshfs/storagebox.pass
```
storagebox.pass里面只放你的StorageBoxd的sftp密码
保存后：
```
chmod 600 /root/.config/sshfs/storagebox.pass
```
5.先手动测试挂载
=============
```
sshfs username@:your-storage-box.domain:/ /mnt/storage \
  -p 22 \  #sftp端口
  -o password_stdin \
  < /root/.config/sshfs/storagebox.pass
```

6.验证是否挂载成功
=============
```
ls -lah /mnt/storage
```
如果能够看到Storage Box的目录，就成功了。

7.（可选）使用脚本快速挂载
=============
1.创建脚本
-------------
```
nano storagebox-sshfs.sh
```
写入：
```
#!/bin/bash

cat /root/.config/sshfs/storagebox.pass | /usr/bin/sshfs \
  username@:your-storage-box.domain:/ /mnt/storage \
  -p 22 \
  -o password_stdin \
  -o reconnect \
  -o ServerAliveInterval=15 \
  -o ServerAliveCountMax=3
```
2.添加执行权限
-------------
```
chmod 700 storagebox-sshfs.sh
```
3.先确保挂载目录存在
-------------
```
ls -lah /mnt/storage  #查看目录是否存在
mkdir -p /mnt/storage  #如果不存在则创建目录
```
4.执行
-------------
```
./storagebox-sshfs.sh
```
如果之前其他方式挂载过，先执行以下命令取消挂载再执行脚本
```
fusermount3 -u /mnt/storage
```
5.验证是否挂载成功
-------------
```
ls -lah /mnt/storage
```
如果能够看到Storage Box的目录，就成功了。