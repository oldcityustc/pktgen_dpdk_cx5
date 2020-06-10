# How to test jumbo frame

The most simple test structure:

cx5-->dut-->cx5



## CX5 config

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

## CX5 mtu config

```shell
ifconfig p6p1 mtu 9600
```

## CX5 pktgen script

especially for the -j parameter, to enable the jumbo.

```shell
sudo echo 10 > /sys/devices/system/node/node0/hugepages/hugepages-1048576kB/nr_hugepages
sudo echo 10 > /sys/devices/system/node/node1/hugepages/hugepages-1048576kB/nr_hugepages
mkdir -p /mnt/huge
mount -t hugetlbfs nodev /mnt/huge


./app/x86_64-native-linuxapp-gcc/pktgen -l 0-11 -n 4 -w d9:00.0 -w d9:00.1 -- -P -j -m "[1:3].0,[7:9].1"
```



## Dut config

on dut , testpmd io mode is used.

script for jumbo , especially for --enable-scatter parameter.

```shell
vf_bdf=$(lspci -d 10ee: | awk 'NR==5 {print $1}')
echo $vf_bdf
./build/app/testpmd_1mac -cf -n4 -w $vf_bdf,desc_prefetch=1,cmpt_desc_len=16 --log-level pmd.net.qdma.init:8 -- -i --nb-cores=1 --rxq=1 --txq=1 --rxd=2048 --txd=2048 --burst=64  --enable-scatter --max-pkt-len=9600 --mbuf-size=4224 --txpkts=64 --eth-peer=0,00:00:00:00:00:00

```





## ethtool

```shell
ethtool -K eth<x> [rx on|off] [tx on|off] [sg on|off] [tso on|off] [lro on|off] [gro on|off] [gso on|off] [rxvlan on|off] [txvlan on|off] [ntuple on/off] [rxhash on/off] [rx-all on/off] [rx-fcs on/off]
```

```
ethtool -k eth<x>
Queries the stateless offload status.
```

