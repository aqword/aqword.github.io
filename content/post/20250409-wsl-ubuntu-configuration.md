---
title: "wsl模式Ubuntu环境配置"
date: "2025-03-20"
description: "wsl个人常用配置"
tags: ["wsl", "ubuntu"]
---

# 0x00 Environment

1. OS：Windows 11 23H2

2. `wsl -v`
```
WSL 版本： 2.4.13.0
内核版本： 5.15.167.4-1
WSLg 版本： 1.0.65
MSRDC 版本： 1.2.5716
Direct3D 版本： 1.611.1-81528511
DXCore 版本： 10.0.26100.1-240331-1435.ge-release
Windows 版本： 10.0.22631.5039
```

# 0x01 Distribution Configuration

1. 到 Microsoft store 中安装 Linux 发行版本，安装完成后，使用 wsl 命令备份

```bat
# 导出
wsl --export <Distro> <FileName>
# 导入
wsl --import <Distro> <InstallLocation> <FileName> 

# 设置默认值
wsl --set-default, -s <Distro>

# 设置版本
wsl --set-version <Distro> <Version>
```

配置wslconfig文件：`%USERPROFILE%\.wslconfig`
```ini
[wsl2]

# 配置网络模式为镜像模式，网络将于宿主机相同
networkingMode=mirrored
# 更改将 DNS 请求从 WSL 代理到 Windows 的方式
dnsTunneling=true

# 如果设置为 true，则 Windows 防火墙规则以及特定于 Hyper-V 流量的规则可以筛选 WSL 网络流量
firewall=true

# 强制 WSL 使用 Windows 的 HTTP 代理信息, 默认为false 是否打开看个人的需要
autoProxy=true

# Sets amount of swap storage space to 8GB, default is 25% of available RAM
swap=8GB

# Sets swapfile path location, default is %USERPROFILE%\AppData\Local\Temp\swap.vhdx
swapfile=C:\\wsl-swap.vhdx
guiApplications=false
memory=25769803776

# 实验性功能配置
[experimental]
# 如果设置为 true，则任何新创建的 VHD 将自动设置为稀疏。
sparseVhd=true

# 仅当 wsl2.networkingMode 设置为 mirrored 时才适用。 如果设置为 True，将会允许容器通过分配给主机的 IP 地址连接到主机，或允许主机通过此方式连接到容器。 请注意，始终可以使用 127.0.0.1 环回地址 - 此选项也允许使用所有额外分配的本地 IP 地址。
# 意味着局域网内别的设备可以通过宿主机ip访问容器
hostAddressLoopback=true

# 仅当 wsl2.networkingMode 设置为 mirrored 时才适用。 指定 Linux 应用程序可以绑定到哪些端口（即使该端口已在 Windows 中使用）。 通过此设置，应用程序能够仅侦听 Linux 中的流量端口，因此即使该端口在 Windows 上用于其他用途，这些应用程序也不会被阻止。 例如，WSL 将允许绑定到 Linux for Docker Desktop 中的端口 53，因为它只侦听来自 Linux 容器中的请求。 应在逗号分隔列表中设置格式，例如：3000,9000,9090
# 默认为Null
ignoredPorts=22
```

# 0x02 Dev Tool Configuration

- 磁盘挂载

```powershell
Write-Output "\\.\PhysicalDrive$((Mount-VHD -Path <pathToVHD> -PassThru | Get-Disk).Number)"
wsl --mount --vhd <pathToVHD>
```

- 开发环境配置

```bash
# /etc/wsl.conf
cat <<EOF >> /etc/wsl.conf
[boot]
systemd=true
# command = service docker start

[network]
hostname=uusrv
generateHosts=false
generateResolvConf=false

EOF

# openssh
apt install openssh-server -y

# python
mkdir ~/.pip
cat <<EOF >> ~/.pip/pip.conf
[global]
timeout = 60
index-url = http://mirrors.cloud.tencent.com/pypi/simple
trusted-host = mirrors.cloud.tencent.com
EOF

# nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.2/install.sh | bash

# pnpm
npm install -g pnpm@latest-10

# npm 阿里源
pnpm config set registry https://registry.npmmirror.com

# golang
wget https://go.dev/dl/go1.24.2.linux-amd64.tar.gz
tar -C /usr/local -xzf go1.24.2.linux-amd64.tar.gz
cat <<EOF >> ~/.bashrc
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin
EOF
source ~/.bashrc
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct

# rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# docker
apt install ca-certificates curl gnupg
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
chmod a+r /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
apt update
apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

```



