# 群晖NAS安装ARIA2下载工具
* 可安装于ARM架构的群晖
* 无需Docker

安装过程需开启群晖的SSH功能，并通过`sudo -i`切换到root用户

## 准备工作（安装Entware，已安装可跳过）

1. 创建文件夹（在rootfs分区外）
```bash
mkdir -p /volume1/@Entware/opt
```

2. 创建optware文件夹`/opt`
确保文件夹是空的（即未安装optware）
```bash
rm -rf /opt
ln -s /volume1/@Entware/opt/ /
```
3. 通过`uname -m`命令查询处理器架构，如DS118的处理器架构为aarch64

4. 根据处理器架构运行安装脚本
* for armv8 (aarch64) - Realtek RTD129x
```bash
wget -O - https://bin.entware.net/aarch64-k3.10/installer/generic.sh | /bin/sh
```
* for armv5
```bash
wget -O - https://bin.entware.net/armv5sf-k3.2/installer/generic.sh | /bin/sh
```
* for armv7
```bash
wget -O - https://bin.entware.net/armv7sf-k3.2/installer/generic.sh | /bin/sh
```
* for x64
```bash
wget -O - https://bin.entware.net/x64-k3.2/installer/generic.sh | /bin/sh
```

5. 重启NAS

6. 更新 Entware 软件包
```bash
/opt/bin/opkg update
```

## 安装ARIA2并设置为自动启动

1. 安装ARIA2
```bash
/opt/bin/opkg install aria2c
#安装ca-certificates，避免出现证书错误
/opt/bin/opkg install ca-certificates
```

2. 配置ARIA2 服务

* 启动vi编辑器
```bash
vi /etc/systemd/system/aria2.service
```

* 按i进入编辑模式，键入以下内容（--conf-path=后面键入aria2配置文件路径，对应的aria2.conf文件内容也需要修改）
```bash
[Unit]
Description=Aria2 daemon, The ultra fast download utility.
After=network.target

[Service]
Type=simple
Restart=always
ExecStart=/opt/bin/aria2c --conf-path=/volume1/homes/.aria2/aria2.conf

[Install]
WantedBy=multi-user.target
```

3.设置系统启动时运行服务
```bash
systemctl enable aria2
```


