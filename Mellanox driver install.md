
# STEPS
### 1, download from here "https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed"
### 2, install "./mlnxofedinstall"
###3, 初始安装后，默认会是“Infiniband controller: Mellanox Technologies”，需要切换到ethnet，参考如下链接
###4，“https://community.mellanox.com/s/article/getting-started-with-connectx-5-100gb-s-adapters-for-linux”


# a. Start MFT.
$ mst start
# b. Extract the vendor_part_id parameter.
$ ibv_devinfo | grep vendor_part_id
# c. Query the Host about ConnectX-4 adapters:
$ mlxconfig -d /dev/mst/mt4119_pciconf0 q
Note that the LINK_TYPE_P1 and LINK_TYPE_P2 equal 1 (InfiniBand) by default.
# d. Change the port type to Ethernet (LINK_TYPE = 2):
$mlxconfig -d /dev/mst/mt4119_pciconf0 set LINK_TYPE_P1=2 LINK_TYPE_P2=2
# e. Reboot the server.


$ ifconfig ens801f0 15.15.15.5/24 up
$ ifconfig ens801f0 mtu 9000

$ ibdev2netdev

mlx5_0 port 1 ==> ens801f0 (Up)

mlx5_1 port 1 ==> ens801f0 (Up)


### sudo mlxlink -d d9:00.0 --fec RS --fec_speed 100G
### sudo mlxlink -d d9:00.1 --fec RS --fec_speed 100G
### sudo ethtool -s p6p1 speed 100000 autoneg off
### sudo ethtool -s p6p2 speed 100000 autoneg off

### sudo mlxlink -d d9:00.0
### sudo mlxlink -d d9:00.1
