# OS tutorial
## 终端配置
字体Cascadia Code
### oh my zsh
```sh
#install zsh
sudo apt install zsh
#check zsh version >=5.0.8
zsh --version
#set default shell
chsh -s $(which zsh)
echo $SHELL
#install ohmyzsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
#network faill
sh -c "$(curl -fsSL https://install.ohmyz.sh/)"
#zsh2000 theme add in ~/.zshrc
ZSH_THEME="zsh2000"
export ZSH_2000_DISABLE_RVM='true'
# theme powerlevel10k
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
ZSH_THEME="powerlevel10k/powerlevel10k"
```

## 实用命令 
### 后台运行程序

1.`subcommand &`终端退出后关闭    
2.`nohup subcommand &`忽略挂起信号，终端退出后还在。   
3.要移动正在运行的前台进程进入后台运行，请先按快捷键`Ctrl+Z`停止进程正在运行的进程。然后运行`bg`命令将停止的进程移至后台运行。  
4.`jobs -l`查看该终端正在运行的进程情况，用`fg n`前台运行,`bg n`后台运行
### 解压压缩命令

```sh
tar -xzf archive.tar.gz #解压tar.gz包
xz -dc libvirt-x.x.x.tar.xz | tar xvf -   #解压tar.xz包
tar -xfJv xxx.tar.xz

```
## 环境配置
### git的子模块
需要 `clone`  git仓库，有时候git仓库会包含**子模块**，直接clone无法下载完整，需要在clone后执行  
```sh
git submodule init  
git submodule update
```
或者用 `git clone –recursive xxx.git`  


## 网络问题

### ubuntu软件源设置

为了加快软件包安装速度，需要配置ubuntu的软件源 `/etc/apt/sources.list`，将文件替换为下面的内容
```sh
#默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释  
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse  
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse  
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse  
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse  
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse  
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse  
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse  
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse  
# 预发布软件源，不建议启用  
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse  
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse  
```
或者使用命令直接替换
```sh
sudo sed -i "s@http://.*archive.ubuntu.com@https://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list
sudo sed -i "s@http://.*security.ubuntu.com@https://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list
```

### rustup镜像网站
rustup是rust的包管理器
```sh
export RUSTUP_DIST_SERVER=https://mirrors.ustc.edu.cn/rust-static
export RUSTUP_UPDATE_ROOT=https://mirrors.ustc.edu.cn/rust-static/rustup
curl https://sh.rustup.rs -sSf | sh  
```
确认一下我们正确安装了 Rust 工具链：
```sh
rustc --version
```
最好把 Rust 包管理器 cargo 镜像地址 crates.io 也替换成中国科学技术大学的镜像服务器，来加速三方库的下载。 打开或新建 ~/.cargo/config 文件，并把内容修改为：
```sh
[source.crates-io]
registry = "https://github.com/rust-lang/crates.io-index"
replace-with = 'ustc'
[source.ustc]
registry = "git://mirrors.ustc.edu.cn/crates.io-index"
```
安装nightly版本
```sh
rustup toolchain install nightly
rustup default nightly
rustup component add rust-src --toolchain nightly-x86_64-unknown-linux-gnu
rustup component add llvm-tools-preview
cargo install cargo-binutils
```
### Qemu 模拟器安装
我们需要使用 Qemu 7.0.0 以上版本进行实验，为此，从源码手动编译安装 Qemu 模拟器：
```sh
# 安装编译所需的依赖包
sudo apt install autoconf automake autotools-dev curl libmpc-dev libmpfr-dev libgmp-dev \
              gawk build-essential bison flex texinfo gperf libtool patchutils bc \
              zlib1g-dev libexpat-dev pkg-config  libglib2.0-dev libpixman-1-dev git tmux python3 ninja-build
wget https://download.qemu.org/qemu-7.0.0.tar.xz
# 解压
tar xvJf qemu-7.0.0.tar.xz
# 编译安装并配置 RISC-V 支持
cd qemu-7.0.0
./configure --target-list=riscv64-softmmu,riscv64-linux-user
make -j$(nproc)
```
### 代理工具proxychains
它的配置文件在 `/etc/proxychains.conf`，修改末尾的ip地址和端口号，wsl2就将主机的ip地址和socks5的端口号填入，主机上就是自己127.0.0.1 。使用方法是在其他命令前加上proxychains,例如`proxychains wget xxxxx`
```sh
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
#socks4 	127.0.0.1 9050
socks5 172.29.176.1 4781
```
### 网络工具curl host 
curl是一个网络工具，可以和服务器进行交互,-v可以显示很多有用的东西，host可以查询域名对应的ip，在很多情况下，虽然配置了http和https代理，但在访问域名时，很多会进行dns查询，这个还不会走代理，也会导致网络问题
所以配置一下域名服务器`/etc/resolv.conf`，改成8.8.8.8或8.8.8.4，加多个也是可以的

### git协议
git://xxxx是git传输协议，git可以通过这种方式来传输数据，不需要登陆验证，传输快，但是在2022年github已经屏蔽了非加密git传输，所以所有用git协议来获取github仓库的都需要修正，可以改用https协议，在依赖bibake文件的构建系统中，加上`protocol=https`就可以

### isar
是西门子的一个构建框架，其中会从deb源下载包，最好换成国内镜像
## 环境变量 搜索路径

### 环境变量问题
将自己的程序路径加入环境变量，在 `~/.bashrc`文件的末尾加上
```sh
export PATH=$PATH:/path/to/myexe
export PATH=/path/to/myexe:$PATH
```
这都是把自己的路径加入到搜寻的路径中，但当路径中存在多个同名文件时，在PATH里排前面的优先级更高，第一个命令把自己的路径排到末尾，第二个排到开头，按需使用。
### 安装包解压后的处理
很多安装包解压出来一堆`bin include lib`文件夹，我们这样做好像就可以
```sh
cd xxx/bin
sudo cp ./* /usr/local/bin/
cd /usr/local/bin/
sudo chmod 777 ./*
```

## 为wsl2添加kvm支持
amd目前做不到，intel的可以通过重新编译内核，再指定wsl加载对应内核实现。

## qemu下进行调试
### 使用GDB

