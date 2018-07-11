# Interface
## Change Interface Name
### [Permanent Solution](https://wiki.archlinux.org/index.php/Network_configuration#Change_interface_name)
```
$ sudo touch /etc/udev/rules.d/10-network.rules
$ sudo bash -c "cat > /etc/udev/rules.d/10-network.rules" << EOF
SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="<INTERFACE_MAC_ADDRESS>", KERNEL=="<ORIGINAL_INTERFACE_NAME>", NAME="<NEW_INTERFACE_NAME>"
EOF
```

E.g.:
```
$ nmcli connection
NAME                UUID                                  TYPE      DEVICE
WLAN-PUB            2cde1f25-8c28-4318-9781-b9fcdabd985d  wifi      wlp2s0
Wired connection 1  f72d569d-065b-3bc8-98ae-e07f8bf46945  ethernet  enp0s31f6
cni0                dcfc10c6-5421-4405-9d56-b3bb595780f5  bridge    cni0
docker0             29822e8f-772f-4e67-8052-55b9e6c9e298  bridge    docker0

$ sudo bash -c "cat > /etc/udev/rules.d/10-network.rules" << EOF
SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="30:e1:71:95:19:bc", NAME="eth0"
SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="34:f3:9a:52:75:65", NAME="wlan0"
EOF

$ sudo reboot

$ nmcli connection
NAME                UUID                                  TYPE      DEVICE
WLAN-PUB            2cde1f25-8c28-4318-9781-b9fcdabd985d  wifi      wlan0
Wired connection 1  f72d569d-065b-3bc8-98ae-e07f8bf46945  ethernet  eth0
cni0                9a2d48d7-e1c7-4fe4-a164-ffde9716dbf3  bridge    cni0
docker0             5db99dac-d17d-4765-9f38-057ff2c853ff  bridge    docker0
```
### [Temporary Solution](http://kernelpanik.net/rename-a-linux-network-interface-without-udev/)
```
sudo ifconfig <ORIGINAL_INTERFACE_NAME> down
sudo ip link set <ORIGINAL_INTERFACE_NAME> name <NEW_INTERFACE_NAME>
sudo ifconfig <NEW_INTERFACE_NAME>
```

E.g.:
```
$ nmcli dev
DEVICE        TYPE      STATE         CONNECTION
wlp2s0        wifi      connected     WLAN-PUB
cni0          bridge    connected     cni0
enp0s31f6     ethernet  connected     Wired connection 1
docker0       bridge    connected     docker0
flannel.1     vxlan     disconnected  --
veth1890b284  ethernet  unmanaged     --
veth5145289b  ethernet  unmanaged     --
vetha9ee773c  ethernet  unmanaged     --
vethf5a48bb2  ethernet  unmanaged     --
lo            loopback  unmanaged     --

$ nmcli connection
NAME                UUID                                  TYPE      DEVICE
WLAN-PUB            2cde1f25-8c28-4318-9781-b9fcdabd985d  wifi      wlp2s0
Wired connection 1  f72d569d-065b-3bc8-98ae-e07f8bf46945  ethernet  enp0s31f6
cni0                dcfc10c6-5421-4405-9d56-b3bb595780f5  bridge    cni0
docker0             29822e8f-772f-4e67-8052-55b9e6c9e298  bridge    docker0

$ sudo ifconfig enp0s31f6 down; sudo ip link set enp0s31f6 name eth0; sudo ifconfig eth0 up

$ nmcli dev
DEVICE        TYPE      STATE         CONNECTION
wlp2s0        wifi      connected     WLAN-PUB
cni0          bridge    connected     cni0
docker0       bridge    connected     docker0
eth0          ethernet  connected     Wired connection 1
flannel.1     vxlan     disconnected  --
veth1890b284  ethernet  unmanaged     --
veth5145289b  ethernet  unmanaged     --
vetha9ee773c  ethernet  unmanaged     --
vethf5a48bb2  ethernet  unmanaged     --
lo            loopback  unmanaged     --

$ nmcli connection
NAME                UUID                                  TYPE      DEVICE
WLAN-PUB            2cde1f25-8c28-4318-9781-b9fcdabd985d  wifi      wlp2s0
Wired connection 1  f72d569d-065b-3bc8-98ae-e07f8bf46945  ethernet  eth0
cni0                e557e9bc-754e-4dc9-b9db-4519a7b15c33  bridge    cni0
docker0             47c195b8-4867-40d3-acec-c28223e2b013  bridge    docker0

```

## Show
### ethtool
```
 $ sudo ethtool eth0
Settings for eth0:
    Supported ports: [ TP ]
    Supported link modes:   10baseT/Half 10baseT/Full
                            100baseT/Half 100baseT/Full
                            1000baseT/Full
    Supported pause frame use: No
    Supports auto-negotiation: Yes
    Supported FEC modes: Not reported
    Advertised link modes:  10baseT/Half 10baseT/Full
                            100baseT/Half 100baseT/Full
                            1000baseT/Full
    Advertised pause frame use: No
    Advertised auto-negotiation: Yes
    Advertised FEC modes: Not reported
    Speed: 100Mb/s
    Duplex: Full
    Port: Twisted Pair
    PHYAD: 1
    Transceiver: internal
    Auto-negotiation: on
    MDI-X: on (auto)
    Supports Wake-on: pumbg
    Wake-on: g
    Current message level: 0x00000007 (7)
                   drv probe link
    Link detected: yes
```

### List Hardware
```
$ sudo lshw -class network
  *-network
       description: Wireless interface
       product: Wireless 8260
       vendor: Intel Corporation
       physical id: 0
       bus info: pci@0000:02:00.0
       logical name: wlan0
       version: 3a
       serial: 34:f3:9a:52:75:65
       width: 64 bits
       clock: 33MHz
       capabilities: pm msi pciexpress bus_master cap_list ethernet physical wireless
       configuration: broadcast=yes driver=iwlwifi driverversion=4.15.0-23-generic firmware=34.0.1 ip=192.168.10.235 latency=0 link=yes multicast=yes wireless=IEEE 802.11
       resources: irq:129 memory:e4100000-e4101fff
  *-network
       description: Ethernet interface
       product: Ethernet Connection (2) I219-LM
       vendor: Intel Corporation
       physical id: 1f.6
       bus info: pci@0000:00:1f.6
       logical name: eth0
       version: 31
       serial: 30:e1:71:95:19:bc
       size: 100Mbit/s
       capacity: 1Gbit/s
       width: 32 bits
       clock: 33MHz
       capabilities: pm msi bus_master cap_list ethernet physical tp 10bt 10bt-fd 100bt 100bt-fd 1000bt-fd autonegotiation
       configuration: autonegotiation=on broadcast=yes driver=e1000e driverversion=3.2.6-k duplex=full firmware=0.8-4 ip=130.147.182.57 latency=0 link=yes multicast=yes port=twisted pair speed=100Mbit/s
       resources: irq:127 memory:e4300000-e431ffff
  *-network:0
       description: Ethernet interface
       physical id: 2
       logical name: veth680e7b1b
       serial: 86:86:ae:b1:3f:c3
       size: 10Gbit/s
       capabilities: ethernet physical
       configuration: autonegotiation=off broadcast=yes driver=veth driverversion=1.0 duplex=full link=yes multicast=yes port=twisted pair speed=10Gbit/s
  *-network:1
       description: Ethernet interface
       physical id: 3
       logical name: vethdd057c06
       serial: ae:b1:42:e3:30:d0
       size: 10Gbit/s
       capabilities: ethernet physical
       configuration: autonegotiation=off broadcast=yes driver=veth driverversion=1.0 duplex=full link=yes multicast=yes port=twisted pair speed=10Gbit/s
  *-network:2
       description: Ethernet interface
       physical id: 4
       logical name: vethfe693595
       serial: 16:ec:cd:4e:c3:d8
       size: 10Gbit/s
       capabilities: ethernet physical
       configuration: autonegotiation=off broadcast=yes driver=veth driverversion=1.0 duplex=full link=yes multicast=yes port=twisted pair speed=10Gbit/s
  *-network:3
       description: Ethernet interface
       physical id: 5
       logical name: flannel.1
       serial: 2a:2b:77:a7:35:8f
       capabilities: ethernet physical
       configuration: broadcast=yes driver=vxlan driverversion=0.1 ip=10.244.0.0 link=yes multicast=yes
  *-network:4
       description: Ethernet interface
       physical id: 6
       logical name: cni0
       serial: 0a:58:0a:f4:00:01
       capabilities: ethernet physical
       configuration: broadcast=yes driver=bridge driverversion=2.3 firmware=N/A ip=10.244.0.1 link=yes multicast=yes
  *-network:5
       description: Ethernet interface
       physical id: 7
       logical name: docker0
       serial: 02:42:99:41:e5:02
       capabilities: ethernet physical
       configuration: broadcast=yes driver=bridge driverversion=2.3 firmware=N/A ip=172.17.0.1 link=no multicast=yes
  *-network:6
       description: Ethernet interface
       physical id: 8
       logical name: vethc1b38134
       serial: 76:6a:6c:0f:e8:a1
       size: 10Gbit/s
       capabilities: ethernet physical
       configuration: autonegotiation=off broadcast=yes driver=veth driverversion=1.0 duplex=full link=yes multicast=yes port=twisted pair speed=10Gbit/s
```

### Show Route
```
$ ip route show
default via 192.168.10.1 dev wlan0 proto dhcp metric 600
10.244.0.0/24 dev cni0 proto kernel scope link src 10.244.0.1
130.140.0.0/16 dev eth0 scope link
130.145.0.0/16 dev eth0 scope link
130.146.0.0/16 dev eth0 scope link
130.147.0.0/16 dev eth0 scope link
130.147.182.0/23 dev eth0 proto kernel scope link src 130.147.182.57 metric 100
137.55.0.0/16 dev eth0 scope link
161.83.0.0/16 dev eth0 scope link
161.84.0.0/16 dev eth0 scope link
161.85.0.0/16 dev eth0 scope link
161.88.0.0/16 dev eth0 scope link
161.91.0.0/16 dev eth0 scope link
161.92.0.0/16 dev eth0 scope link
169.254.0.0/16 dev eth0 scope link metric 1000
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
185.166.0.0/16 dev eth0 scope link
192.168.10.0/23 dev wlan0 proto kernel scope link src 192.168.10.235 metric 600
```

OR
```
$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.10.1    0.0.0.0         UG    600    0        0 wlan0
10.244.0.0      0.0.0.0         255.255.255.0   U     0      0        0 cni0
130.140.0.0     0.0.0.0         255.255.0.0     U     0      0        0 eth0
130.145.0.0     0.0.0.0         255.255.0.0     U     0      0        0 eth0
130.146.0.0     0.0.0.0         255.255.0.0     U     0      0        0 eth0
130.147.0.0     0.0.0.0         255.255.0.0     U     0      0        0 eth0
130.147.182.0   0.0.0.0         255.255.254.0   U     100    0        0 eth0
137.55.0.0      0.0.0.0         255.255.0.0     U     0      0        0 eth0
161.83.0.0      0.0.0.0         255.255.0.0     U     0      0        0 eth0
161.84.0.0      0.0.0.0         255.255.0.0     U     0      0        0 eth0
161.85.0.0      0.0.0.0         255.255.0.0     U     0      0        0 eth0
161.88.0.0      0.0.0.0         255.255.0.0     U     0      0        0 eth0
161.91.0.0      0.0.0.0         255.255.0.0     U     0      0        0 eth0
161.92.0.0      0.0.0.0         255.255.0.0     U     0      0        0 eth0
169.254.0.0     0.0.0.0         255.255.0.0     U     1000   0        0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
185.166.0.0     0.0.0.0         255.255.0.0     U     0      0        0 eth0
192.168.10.0    0.0.0.0         255.255.254.0   U     600    0        0 wlan0
```

# DNS
## Permanently Add new DNS
```
$ sudo apt install resolvconf
$ sudo cp /etc/resolvconf/resolv.conf.d/head{,.org}
$ sudo bash -c "cat > /etc/resolvconf/resolv.conf.d/head" << EOF
nameserver 130.147.236.5
nameserver 161.92.35.78
search cn-132.lan.philips.com
EOF

$ sudo resolvconf -u

$ cat /etc/resolv.conf
nameserver 127.0.0.53

$ sudo resolvconf -u

$ cat /etc/resolv.conf
nameserver 130.147.236.5
nameserver 161.92.35.78
nameserver 127.0.0.53
search cn-132.lan.philips.com
```

# Reference
- [ARCH NetWork Configuration](https://wiki.archlinux.org/index.php/Network_configuration#Change_device_name)