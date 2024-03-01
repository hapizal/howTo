
CREATE BONDING NETWORK INTERFACE WITH VLAN TAG

_This is howto when we need to create or seperate trunk mode interface to several vlan interfaces_


_Step 1_
_Enable the driver for this type of interface mode_

```
lsmod | grep 8021q
modprobe 8021q
echo "8021q" >> /etc/modules
```



Step2 
create main bond0 interface 
create bond interface only without IP parameters
```
[root@IAM network-scripts]# cat ifcfg-bond0
DEVICE=bond0
BOOTPROTO=none
ONBOOT=yes
TYPE=Ethernet
BONDING_OPTS="mode=1 miimon=100"  # config it as fault-tolerance (active-backup)
```


/// create bond0 vlan tagged 
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

///config ens2f1 and ens2f0 as usual


//add route 
```
[root@IAM network-scripts]# cat route-bond0.10
default via 10.41.14.62 dev bond0 table 1
[root@IAM network-scripts]#
[root@IAM network-scripts]# cat route-bond0.30
default via 10.41.14.254 dev bond0.30 table 2
[root@IAM network-scripts]#
[root@IAM network-scripts]#
```

/// add rule 
```
[root@IAM network-scripts]# cat rule-bond0.10
from 10.41.14.0/26 tab 1 priority 500
[root@IAM network-scripts]#
[root@IAM network-scripts]# cat rule-bond0.30
from 10.41.14.240/28 tab 2 priority 501
[root@IAM network-scripts]#
[root@IAM network-scripts]#
```

// add net rule 
```
[root@IAM network-scripts]# cat /etc/sysctl.d/90-override.conf
net.ipv4.ip_forward=1
net.ipv4.conf.all.arp_filter=0
net.ipv4.conf.all.rp_filter=2
[root@IAM network-scripts]#
```

```
sysctl -p /etc/sysctl.d/90-override.conf
```
