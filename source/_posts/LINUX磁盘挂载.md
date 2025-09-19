---
title: LINUX磁盘挂载
date: 2025-09-19 09:13:15
tags: [LINUX]
---


# 临时挂载磁盘

## 先创建文件夹作为挂载点
## 再用一下相关命令执行挂载与卸载**磁盘与文件系统**


| 命令 | 说明 |
| ---- | ---- |
| lsblk | 查看块设备 |
| mount /dev/sdb1 /mnt | 挂载磁盘 |
| umount /mnt | 卸载挂载 |
| fdisk -l | 查看磁盘分区 |
| mkfs.ext4 /dev/sdb1 | 格式化分区 |
<!--more-->

# 永久挂载磁盘

## 1. lsblk -f 查看磁盘uuid等相关信息


```bash
lsblk -f

```

输出示例：


```pgsql
NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
sda
├─sda1 ext4         123e4567-e89b-12d3-a456-426614174000 /
├─sda2 xfs          987e6543-e21b-32d3-a654-426614174999 /data

```

👉 直观显示分区、文件系统、UUID、挂载点。


## 2.修改fstab文件




### 1. 什么是 fstab


- `fstab` 全称 **File Systems Table**（文件系统表）。
- 位置：`/etc/fstab`
- 作用：定义 **系统启动时自动挂载**的文件系统，也可以给手动挂载 (`mount`) 提供简化配置。

换句话说，它就像一张“挂载清单”，告诉系统：哪些分区、设备、网络存储要挂载到哪里、用什么方式挂载。



### 2. fstab 文件格式


每一行描述一个挂载点，通常有 **6 个字段**：


```php-template
                    

```

#### 字段说明


1. **device** → 挂载的设备或 UUID

- 例：`/dev/sda1` 或 `UUID=xxxx-xxxx`
- 建议使用 `UUID`，避免因为设备顺序变化导致挂载错乱。
2. **mount_point** → 挂载点

- 例：`/`、`/boot`、`/data`
3. **fs_type** → 文件系统类型

- 常见：`ext4`、`xfs`、`swap`、`nfs`、`vfat`、`iso9660`
4. **options** → 挂载选项（逗号分隔）

- `defaults` → 默认（rw, suid, dev, exec, auto, nouser, async）
- `ro` → 只读
- `rw` → 读写
- `noexec` → 禁止执行二进制文件
- `nosuid` → 禁止 SUID/SGID 位生效
- `nodev` → 禁止设备文件
- `noauto` → 不随开机自动挂载
- `user` / `nouser` → 是否允许普通用户挂载
- `relatime` → 相对访问时间更新（比 `atime` 高效）
5. **dump** → 备份相关（几乎不用）

- `0` → 不用 dump 备份
- `1` → dump 会备份
6. **pass** → 开机时 fsck 检查顺序

- `0` → 不检查
- `1` → 通常根分区 `/`
- `2` → 其他分区（按顺序检查）


### 3. 示例


#### 本地分区


```fstab
UUID=xxxx-xxxx   /       ext4    defaults        1 1
UUID=yyyy-yyyy   /data   xfs     defaults        0 2

```

#### swap 分区


```fstab
UUID=zzzz-zzzz   none    swap    sw              0 0

```

#### 光盘镜像


```fstab
/dev/cdrom       /mnt/cdrom   iso9660   ro,user,noauto   0 0

```

#### 网络文件系统 (NFS)


```fstab
192.168.1.10:/share   /mnt/nfs   nfs   defaults   0 0

```


### 4. 常用操作


1. **编辑配置**

```bash
sudo vim /etc/fstab

```
2. **立即生效（挂载全部）**

```bash
sudo mount -a

```
👉 建议修改后先执行这条命令测试是否正确，否则开机可能卡死。
3. **查看当前挂载点**

```bash
mount | column -t
df -h

```


### 5. 注意事项


- 一定要用 `UUID`，而不是 `/dev/sdX`，避免磁盘顺序变动。
- 修改 `fstab` 后，务必运行 `mount -a` 测试，否则系统可能无法启动。
- 对于服务器，挂载网络存储（如 NFS、CIFS）时要小心，网络不可用会导致开机卡死 → 可以加 `nofail` 或 `x-systemd.automount` 选项。


✅ 总结：
`/etc/fstab` 就是 **Linux 的文件系统挂载配置表**，决定系统开机时哪些分区、磁盘、网络存储会挂载到哪里，用什么参数挂载。它是运维必备知识点，配不好可能导致系统无法启动。
