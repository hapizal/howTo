# Create Bonding Network Interfaces with VLAN Tags


_This is howto when we need to create or seperate trunk mode interface to several vlan interfaces_


_Step 1_\
_Enable the driver for this type of interface mode_

```
lsmod | grep 8021q
modprobe 8021q
echo "8021q" >> /etc/modules
```



_Step 2_\
_create main bond0 interface_\ 
_create bond interface only without IP parameters_
```
[root@IAM network-scripts]# cat ifcfg-bond0
DEVICE=bond0
BOOTPROTO=none
ONBOOT=yes
TYPE=Ethernet
BONDING_OPTS="mode=1 miimon=100"  # config it as fault-tolerance (active-backup)
```


_Step 3_\
_create bond0 vlan tagged _
```
[root@IAM network-scripts]# cat ifcfg-bond0.10
# Native interface for VLAN 10 (tagged)
DEVICE=bond0.10
BOOTPROTO=static
IPADDR=10.41.14.16
NETMASK=255.255.255.192
ONBOOT=yes
VLAN=yes
GATEWAY=10.41.14.62
[root@IAM network-scripts]#
```

```
[root@IAM network-scripts]# cat ifcfg-bond0.30
# Native interface for VLAN 30 (tagged)
DEVICE=bond0.30
BOOTPROTO=static
IPADDR=10.41.14.247
NETMASK=255.255.255.240
ONBOOT=yes
VLAN=yes
GATEWAY=10.41.14.254
[root@IAM network-scripts]#
```

_Step 4_\
_config ens2f1 and ens2f0 as usual_\
such as make on_boot=yes and change dhcp to static

_Step 5_\
_Config route for the vlan interfaces_
```
[root@IAM network-scripts]# cat route-bond0.10
default via 10.41.14.62 dev bond0 table 1
[root@IAM network-scripts]#
[root@IAM network-scripts]# cat route-bond0.30
default via 10.41.14.254 dev bond0.30 table 2
[root@IAM network-scripts]#
[root@IAM network-scripts]#
```

_Step 6_\
_Add rule for vlan interfaces_
```
[root@IAM network-scripts]# cat rule-bond0.10
from 10.41.14.0/26 tab 1 priority 500
[root@IAM network-scripts]#
[root@IAM network-scripts]# cat rule-bond0.30
from 10.41.14.240/28 tab 2 priority 501
[root@IAM network-scripts]#
[root@IAM network-scripts]#
```

_Step 7_\
_Add net rule in sysctl_
```
[root@IAM network-scripts]# cat /etc/sysctl.d/90-override.conf
net.ipv4.ip_forward=1
net.ipv4.conf.all.arp_filter=0
net.ipv4.conf.all.rp_filter=2
[root@IAM network-scripts]#
```

_update sysctl_
```
sysctl -p /etc/sysctl.d/90-override.conf
```
