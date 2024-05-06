# OpenWrt Plus 23.05

### NanoPi R4S/R5S/R5C & X86_64 固件下载:

https://github.com/pmkol/openwrt-plus/releases

```
【首次登陆】
地址：10.0.0.1（默认）
用户：root
密码：空

【分区挂载】
系统/磁盘管理 将系统盘剩余空间创建新分区
系统/挂载点   启用新分区并挂载至/opt目录
```

---------------

#### 固件编译脚本存档来自：https://init2.cooluc.com 

- 优化系统内核 [ √ Full cone NAT, √ BBRv3, √ LLVM-BPF, √ Shortcut-FE ]
- 使用 OpenWrt+ImmortalWrt 软件源，支持更多插件的在线安装与升级
- 最小化集成常用插件，修复多处上游插件BUG

| ⚓ 服务 | 🗳️ Docker | 🔗 网络存储 | 🩺 网络 |
| :---- |  :----  |  :----  |  :----  |
| PassWall</br>OpenClash</br>MosDNS</br>硬盘休眠</br>Watchcat</br>Aira2</br>FRP客户端</br>网络共享</br>网络唤醒</br>ZeroTier | Dockerman</br>Docker</br>Dockerd</br>Docker-compose</br></br></br></br></br></br></br> | Alist文件列表</br>USB打印服务器</br>GoWebDav</br></br></br></br></br></br></br></br> | 网速测试</br>SQM队列管理</br>UPnP</br>带宽监控</br>Socat</br>访问控制</br>IP限速</br></br></br></br> |

自定义预装插件建议fork上游原项目，以免因本项目未及时同步上游内核导致编译失败

---------------

### 本地编译环境安装（根据 debian 11 / ubuntu 22）
```shell
sudo apt-get update
sudo apt-get install -y build-essential flex bison g++ gawk gcc-multilib g++-multilib gettext git libfuse-dev libncurses5-dev libssl-dev python3 python3-pip python3-ply python3-distutils python3-pyelftools rsync unzip zlib1g-dev file wget subversion patch upx-ucl autoconf automake curl asciidoc binutils bzip2 lib32gcc-s1 libc6-dev-i386 uglifyjs msmtp texinfo libreadline-dev libglib2.0-dev xmlto libelf-dev libtool autopoint antlr3 gperf ccache swig coreutils haveged scons libpython3-dev jq
```

##### 安装 clang-15 - 启用 BPF 支持时需要
##### 一些过旧的发行版没有提供 clang-15，可以通过 llvm 官方提供源安装：https://apt.llvm.org
```shell
# debian 11
sudo sh -c 'echo "deb http://apt.llvm.org/bullseye/ llvm-toolchain-bullseye-15 main" >> /etc/apt/sources.list'
sudo sh -c 'echo "deb-src http://apt.llvm.org/bullseye/ llvm-toolchain-bullseye-15 main" >> /etc/apt/sources.list'
wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install -y clang-15

# ubuntu 20.04
sudo sh -c 'echo "deb http://apt.llvm.org/focal/ llvm-toolchain-focal-15 main" >> /etc/apt/sources.list'
sudo sh -c 'echo "deb-src http://apt.llvm.org/focal/ llvm-toolchain-focal-15 main" >> /etc/apt/sources.list'
wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install -y clang-15

# debian 12 or latest & ubuntu 22 or latest
sudo apt-get update
sudo apt-get install -y clang-15
```

---------------

### 启用 [glibc](https://www.gnu.org/software/libc/) （测试）
##### 脚本支持使用 glibc 库进行构建，当启用 glibc 进行构建时，构建的固件将会同时兼容 musl/glibc 的预构建二进制程序
##### 只需在构建固件前执行以下命令即可启用 glibc 构建

```
export USE_GLIBC=y
```

### 启用 [GCC13](https://gcc.gnu.org/gcc-13/)/[GCC14](https://gcc.gnu.org/gcc-14/)/[GCC15](https://gcc.gnu.org/gcc-15/) 工具链编译
##### 只需在构建固件前执行以下命令即可启用 GCC13/GCC14/GCC15 工具链

```
# GCC13
export USE_GCC13=y
```

```
# GCC14
export USE_GCC14=y
```

```
# GCC15
export USE_GCC15=y
```

### 启用 [LTO](https://gcc.gnu.org/onlinedocs/gccint/LTO-Overview.html) 优化
##### 只需在构建固件前执行以下命令即可启用编译器 LTO 优化

```
export ENABLE_LTO=y
```

### 启用 [MOLD](https://github.com/rui314/mold) 现代链接器（需要启用 `USE_GCC13=y` 或 `USE_GCC14=y` 或 `USE_GCC15=y`）
##### 只需在构建固件前执行以下命令即可启用 MOLD 链接，如果使用它建议同时启用 LTO 优化

```
export USE_MOLD=y
```

### 启用 [eBPF](https://docs.kernel.org/bpf/) 支持
##### 只需在构建固件前执行以下命令即可启用 eBPF 支持

```
export ENABLE_BPF=y
```

### 启用 [LRNG](https://github.com/smuellerDD/lrng)
##### 只需在构建固件前执行以下命令即可启用 LRNG 内核随机数支持

```
export ENABLE_LRNG=y
```

### 快速构建（仅限 Github Actions）
##### 脚本会使用 [toolchain](https://github.com/sbwml/toolchain-cache) 缓存代替源码构建，与常规构建相比能节省大约 60 分钟的编译耗时，仅适用于 Github Actions `ubuntu-22.04` 环境
##### 只需在构建固件前执行以下命令即可启用快速构建

```
export BUILD_FAST=y
```

### 构建 Minimal 版本
##### 不包含第三方插件，接近官方 OpenWrt 固件
##### 只需在构建固件前执行以下命令即可构建 Minimal 版本

```
export MINIMAL_BUILD=y
```

### 更改 LAN IP 地址
##### 自定义默认 LAN IP 地址
##### 只需在构建固件前执行以下命令即可覆盖默认 LAN 地址（默认：10.0.0.1）

```
export LAN=10.0.0.1
```

---------------

## 构建 OpenWrt 23.05 最新 Releases

### x86_64
```shell
# linux-6.6
bash <(curl -sS https://init2.cooluc.com/build.sh) rc2 x86_64
```

## 构建 OpenWrt 23.05 开发版（23.05-SNAPSHOT）

### x86_64
```shell
# linux-6.6
bash <(curl -sS https://init2.cooluc.com/build.sh) dev x86_64
```

-----------------

# 基于本仓库进行自定义构建 - 本地编译

#### 如果你有自定义的需求，建议不要变更内核版本号，这样构建出来的固件可以直接使用 `opkg install kmod-xxxx`

### 一、Fork 本仓库到自己 GitHub 存储库

### 二、修改构建脚本文件：`openwrt/build.sh`（使用 Github Actions 构建时无需更改）

将 init.cooluc.com 脚本默认连接替换为你的 github raw 连接（不带 https://），像这样 `raw.githubusercontent.com/你的用户名/r4s_build_script/master`

```diff
 # script url
 if [ "$isCN" = "CN" ]; then
-    export mirror=init.cooluc.com
+    export mirror=raw.githubusercontent.com/你的用户名/r4s_build_script/master
 else
-    export mirror=init2.cooluc.com
+    export mirror=raw.githubusercontent.com/你的用户名/r4s_build_script/master
 fi
```

### 三、在本地 Linux 执行基于你自己仓库的构建脚本，即可编译所需固件

#### x86_64 openwrt-23.05
```shell
# linux-6.6
bash <(curl -sS https://raw.githubusercontent.com/你的用户名/r4s_build_script/master/openwrt/build.sh) rc2 x86_64
```

-----------------

# 使用 Github Actions 构建

### 一、Fork 本仓库到自己 GitHub 存储库

### 二、点击仓库右上角的 ⭐Star 既可触发构建
