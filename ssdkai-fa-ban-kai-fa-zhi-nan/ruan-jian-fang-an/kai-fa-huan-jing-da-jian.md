# **开发环境搭建**

推荐使用 ubuntu 16.04 64 位或者 ubuntu 14.04 64 位。

### 工具链安装

安装OpenWRT 编译工具链，对应在SDK 中名称为：

toolchain-arm\_cortex-a7+neon-vfpv4\_gcc-5.3.0\_glibc-2.22\_eabi.tar.gz

toolchain-arm\_cortex-a7 neon-vfpv4\_gcc-5.3.0\_musl-1.1.16\_eabi.tar

通过tar zxvf 命令解压后，放到某个目录下，如/opt/

然后在用户的~/.bashrc 文件内添加该路径到path 变量：

export

PATH="$PATH:/opt/toolchain-arm\_cortex-a7+neon-vfpv4\_gcc-5.3.

0\_musl-1.1.16\_eabi/bin"

### 搭建TFTP 服务器

1.安装服务：sudo apt-get install tftpd-hpa

2.配置：编辑/etc/default/tftpd-hpa 文件

TFTP\_USERNAME="tftp"

TFTP\_DIRECTORY="/opt/tftpboot"

TFTP\_ADDRESS="0.0.0.0:69"

TFTP\_OPTIONS="--secure"

TFTP\_OPTIONS="-l -c -s"

可根据需求修改tftp 目录 TFTP\_DIRECTORY

3.重启tftp 服务：

sudo service tftpd-hpa restart

### 搭建NFS 服务器

1.安装服务：sudo apt-get install nfs-kernel-server

2.配置：编辑/etc/exports 文件

/opt

192.168.\*\(rw,sync,crossmnt,no\_subtree\_check,no\_root\_squash\)

以上配置使/opt 目录及子目录可被192.168.\*网段的nfs 客户端访问，可根据实际情  
况配置。

3.重启nfs 服务：sudo service nfs-kernel-server restart

