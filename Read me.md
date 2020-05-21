# READ ME

## download

method 1:

git clone https://github.com/oldcityustc/pktgen_dpdk_cx5.git

method 2:

download dpdk20.02 from http://core.dpdk.org/download/

download pktgen20.03 from https://git.dpdk.org/apps/pktgen-dpdk

modify dpdk-20.02\config\common_base to support mellanox CX5.

```shell
# ConnectX-6 & BlueField (MLX5) PMD
#
CONFIG_RTE_LIBRTE_MLX5_PMD=n
CONFIG_RTE_LIBRTE_MLX5_DEBUG=n

to 

# ConnectX-6 & BlueField (MLX5) PMD
#
CONFIG_RTE_LIBRTE_MLX5_PMD=y
CONFIG_RTE_LIBRTE_MLX5_DEBUG=n
```

modify dpdk-20.02\config\common_base to support capture.

```shell
# Compile software PMD backed by PCAP files
#
CONFIG_RTE_LIBRTE_PMD_PCAP=n

to

# Compile software PMD backed by PCAP files
#
CONFIG_RTE_LIBRTE_PMD_PCAP=y
```

modify pktgen-20.03.0\app\pktgen-port-cfg.c	to support jumbo frame.	

```c

if (pktgen.enable_jumbo > 0) {
			conf.rxmode.max_rx_pkt_len = pktgen.eth_max_pkt;
			conf.rxmode.offloads |= DEV_RX_OFFLOAD_JUMBO_FRAME;
			conf.txmode.offloads |= DEV_TX_OFFLOAD_MULTI_SEGS;
		}
		
		to
		
if (pktgen.enable_jumbo > 0) {
			conf.rxmode.max_rx_pkt_len = pktgen.eth_max_pkt;
			conf.rxmode.offloads |= DEV_RX_OFFLOAD_JUMBO_FRAME;
			conf.txmode.offloads |= DEV_TX_OFFLOAD_MULTI_SEGS;
			conf.rxmode.offloads |= DEV_RX_OFFLOAD_SCATTER;
		}
```



## install

compile dpdk

```
cd dpdk-20.02/usertools
sudo ./dpdk-setup.sh
```

choose option 38 

```

[37] x86_64-native-linuxapp-clang
[38] x86_64-native-linuxapp-gcc
[39] x86_64-native-linuxapp-icc
[40] x86_64-native-linux-clang
[41] x86_64-native-linux-gcc
[42] x86_64-native-linux-icc


[62] Exit Script

Option:
```

compile pktgen:

```shell
export RTE_SDK=/xx/xx/dpdk/dpdk-20.02
export RTE_TARGET=x86_64-native-linuxapp-gcc
cd pktgen-20.03.0
make
```



## run script

run below cmds to start the pktgen app.

```shell
sudo echo 10 > /sys/devices/system/node/node0/hugepages/hugepages-1048576kB/nr_hugepages
sudo echo 10 > /sys/devices/system/node/node1/hugepages/hugepages-1048576kB/nr_hugepages
mkdir -p /mnt/huge
mount -t hugetlbfs nodev /mnt/huge


./app/x86_64-native-linuxapp-gcc/pktgen -l 0-11 -n 4 -w d9:00.0 -w d9:00.1 -- -P -j -m "[1:3].0,[7:9].1"

```



## pktgen usage：

set port 0 sip to 192.168.1.0，set port0 dip from 192.168.2.0 to 192.168.2.255.

```shell
range 0 src ip 192.168.1.0 192.168.1.0 192.168.1.0 0.0.0.1
range 0 dst ip 192.168.2.0 192.168.2.0 192.168.2.255 0.0.0.1
enable 0 range
start 0
```

set range packet length:

```
range 0 size 512 512 512 0
```

capture:

```
enable 0 capture
disable 0 capture
```



## System requirements

### hugepage support for ubuntu:

vim /etc/default/grub

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet default_hugepagesz=1GB hugepagesz=1G hugepages=50 iommu=pt  intel_iommu=on audit=0 nosoftlockup "
GRUB_DISABLE_RECOVERY="true"

```

```shell
sudo update-grub
```

### GRUB:

```shell
#grub2-mkconfig -o /boot/grub2/grub.cfg
grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
grub2-mkconfig -o /boot/efi/EFI/ubuntu/grub.cfg
```

### LUA

download lua
http://www.lua.org/ftp/

install lua

```shell
make linux
sudo make install
make local
```

### libpcap

Below error you can solve through install libpcap-dev.(-dev package will install head files.)

```
/home/xilinx/pktgen/pktgen-20.03.0/app/pktgen-port-cfg.h:19:23: fatal error: pcap/pcap.h: No such file or directory
```

```
sudo apt install libpcap-dev
```

### libtinfo

Below error you can solve through install libtinfo-dev

```
lua.c:82:31: fatal error: readline/readline.h: No such file or directory
```

```
sudo apt install libtinfo-dev
```

### libnuma

```
apt install libnuma-dev
```

### GCC version

latest GCC version is 7+ after install build-seeential.  need to downgrade to 5.

```shell
sudo apt install build-essential

sudo apt-get install gcc-5

cd /usr/bin/
ll gcc

sudo rm gcc
sudo ln -s gcc-5 gcc
gcc -v
```

