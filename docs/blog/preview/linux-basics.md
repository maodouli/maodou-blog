---
title: Linux 基础知识
tags:
  - linux
  - 操作系统
createTime: 2025/11/16 19:13:00
permalink: /blog/68apz2db/
---

# Linux 基础知识指南

## Linux 简介

Linux 是一个开源的类 Unix 操作系统内核，广泛应用于服务器、嵌入式设备和桌面系统。

### 主要发行版

| 发行版 | 特点 | 适用场景 |
|--------|------|---------|
| Ubuntu | 用户友好，社区活跃 | 桌面、服务器 |
| CentOS | 企业级稳定 | 服务器、企业应用 |
| Debian | 稳定可靠 | 服务器、嵌入式 |
| Fedora | 技术前沿 | 开发、测试 |
| Arch Linux | 滚动更新，高度定制 | 高级用户 |

## 系统安装

### 安装方式选择

1. **虚拟机安装**（推荐新手）
   - VirtualBox
   - VMware Workstation
   - Hyper-V

2. **双系统安装**
   - 与 Windows/macOS 共存
   - 需要分区操作

3. **云服务器**
   - AWS EC2
   - 阿里云 ECS
   - 腾讯云 CVM

### 安装步骤（以 Ubuntu 为例）

```bash
# 1. 下载 ISO 镜像
# 2. 创建启动盘或虚拟机
# 3. 启动安装程序
# 4. 分区和配置
# 5. 完成安装
```

## 基本命令

### 文件和目录操作

```bash
# 列出文件
ls
ls -l    # 详细列表
ls -a    # 显示隐藏文件

# 切换目录
cd /path/to/directory
cd ..    # 上级目录
cd ~     # 家目录

# 创建目录
mkdir directory_name
mkdir -p path/to/directory  # 创建多级目录

# 删除文件/目录
rm file_name
rm -r directory_name  # 递归删除目录
rm -f file_name       # 强制删除

# 复制文件
cp source_file destination_file
cp -r source_dir destination_dir  # 复制目录

# 移动/重命名文件
mv old_name new_name
mv file_name /path/to/destination/

# 查看文件内容
cat file_name
less file_name    # 分页查看
head -n 10 file_name  # 查看前10行
tail -n 10 file_name  # 查看后10行
```

### 系统信息查询

```bash
# 系统信息
uname -a          # 内核信息
cat /etc/os-release  # 发行版信息

# 硬件信息
lscpu             # CPU信息
free -h           # 内存使用情况
df -h             # 磁盘空间

# 进程信息
ps aux            # 所有进程
top               # 实时进程监控
htop              # 增强版top

# 网络信息
ifconfig          # 网络接口信息
ip addr           # 现代替代命令
netstat -tuln     # 端口监听情况
```

### 用户和权限管理

```bash
# 用户管理
whoami            # 当前用户
id                # 用户信息
sudo command      # 以管理员权限执行

# 文件权限
chmod 755 file_name    # 修改权限
chown user:group file_name  # 修改所有者
chgrp group file_name  # 修改所属组

# 权限说明
# r=4 (读), w=2 (写), x=1 (执行)
# 755 = rwxr-xr-x (所有者读写执行，组和其他读执行)
```

## 包管理

### Ubuntu/Debian (apt)

```bash
# 更新包列表
sudo apt update

# 升级已安装包
sudo apt upgrade

# 安装软件包
sudo apt install package_name

# 卸载软件包
sudo apt remove package_name

# 搜索软件包
apt search keyword

# 查看包信息
apt show package_name
```

### CentOS/RHEL (yum/dnf)

```bash
# 更新系统
sudo yum update
sudo dnf update   # 新版本

# 安装软件包
sudo yum install package_name
sudo dnf install package_name

# 卸载软件包
sudo yum remove package_name

# 搜索软件包
yum search keyword
```

## 文本编辑

### Vim 编辑器

```bash
# 基本操作
vim file_name     # 打开文件
i                 # 插入模式
ESC               # 退出插入模式
:w                # 保存
:q                # 退出
:wq               # 保存并退出
:q!               # 强制退出不保存

# 移动光标
h j k l           # 左 下 上 右
0                 # 行首
$                 # 行尾
gg                # 文件开头
G                 # 文件末尾
```

### Nano 编辑器（新手友好）

```bash
nano file_name    # 打开文件
Ctrl+O            # 保存
Ctrl+X            # 退出
Ctrl+W            # 搜索
```

## 网络配置

### 基本网络命令

```bash
# 测试网络连接
ping google.com

# 查看路由表
route -n
ip route

# DNS 查询
nslookup domain.com
dig domain.com

# 下载文件
wget http://example.com/file.zip
curl -O http://example.com/file.zip
```

### SSH 远程连接

```bash
# 连接到远程服务器
ssh username@hostname
ssh -p port username@hostname  # 指定端口

# 生成 SSH 密钥
ssh-keygen -t rsa -b 4096

# 复制公钥到服务器
ssh-copy-id username@hostname
```

## 系统服务管理

### systemd 服务管理

```bash
# 启动服务
sudo systemctl start service_name

# 停止服务
sudo systemctl stop service_name

# 重启服务
sudo systemctl restart service_name

# 查看服务状态
sudo systemctl status service_name

# 启用开机启动
sudo systemctl enable service_name

# 禁用开机启动
sudo systemctl disable service_name
```

### 传统服务管理 (SysVinit)

```bash
# 启动服务
sudo service service_name start

# 停止服务
sudo service service_name stop

# 重启服务
sudo service service_name restart

# 查看服务状态
sudo service service_name status
```

## 磁盘管理

### 磁盘操作

```bash
# 查看磁盘使用情况
df -h

# 查看目录大小
du -sh /path/to/directory

# 挂载磁盘
sudo mount /dev/sdb1 /mnt/mydisk

# 卸载磁盘
sudo umount /mnt/mydisk

# 格式化磁盘
sudo mkfs.ext4 /dev/sdb1
```

## 日志查看

```bash
# 系统日志
sudo tail -f /var/log/syslog
sudo journalctl -f  # systemd 日志

# 应用日志
sudo tail -f /var/log/nginx/error.log
sudo tail -f /var/log/apache2/error.log
```

## 性能监控

### 实时监控工具

```bash
# 系统资源监控
top
htop

# I/O 监控
iotop

# 网络监控
iftop
nethogs
```

## 安全基础

### 防火墙配置

```bash
# UFW (Ubuntu)
sudo ufw enable
sudo ufw allow ssh
sudo ufw allow 80/tcp
sudo ufw status

# firewalld (CentOS)
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload
```

### 定期更新

```bash
# Ubuntu/Debian
sudo apt update && sudo apt upgrade

# CentOS/RHEL
sudo yum update
```

## 学习资源

### 推荐学习路径

1. **基础命令** - 文件操作、文本编辑
2. **系统管理** - 用户权限、服务管理
3. **网络配置** - SSH、防火墙
4. **脚本编程** - Shell 脚本
5. **服务器管理** - Web服务器、数据库

### 在线资源

- [Linux 命令大全](https://man.linuxde.net/)
- [鸟哥的 Linux 私房菜](http://linux.vbird.org/)
- [Linux 中国](https://linux.cn/)

**祝您 Linux 学习之旅顺利！**