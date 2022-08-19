# Networking topics

## Listing network interfaces on Ubuntu

- `ip addr` &rightarrow; generally
- `ip -4 a` &rightarrow; list the IP4 interfaces only

``` {bash}
markus@machine:~$ ip -4 a  
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
inet 127.0.0.1/8 scope host lo
   valid_lft forever preferred_lft forever
4: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
inet 192.168.178.119/24 brd 192.168.178.255 scope global dynamic noprefixroute br0
   valid_lft 167578sec preferred_lft 167578sec
5: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
   valid_lft forever preferred_lft forever
```

---

## Troubleshooting a network bridge connection

[source](https://www.cyberciti.biz/faq/ubuntu-20-04-add-network-bridge-br0-with-nmcli-command/)  
I have set up a bridge interface on my notebook such that my home router (Fitz box) will assign an
individual IP address to each virtual guest machine (KVM/QEMU) on system as common practice.
Recently the bridge was disabled - don't know the reason - maybe related to me using the wifi interface
instead of the ethernet cable connection?

A quick investigation into the infrastructure reveals:

``` {bash}
markus@machine:~$ nmcli connection show --active
NAME                UUID                                  TYPE      DEVICE 
br0                 47d3351f-a425-4cc5-a7d6-08469c7c280c  bridge    br0    
Wired connection 1  79066486-e007-4326-b22b-d9c3acecf2b9  ethernet  enp2s0
```

however the "Wired connection 1" should not be active at all and blocks the bridge interface from doing its job:

``` {bash}
markus@machine:~$ nmcli 
enp2s0: connected to Wired connection 1
        "Realtek RTL8111/8168/8411"
        ethernet (r8169), D4:5D:64:68:9D:53, hw, mtu 1500
        ip4 default
        inet4 192.168.178.119/24
        route4 192.168.178.0/24

br0: connecting (getting IP configuration) to br0
        "br0"
        bridge, F6:FA:38:A0:A9:37, sw, mtu 1500
```

The bridge slave interface is still there as well, but disconnected:

``` {bash}
markus@machine:~$ nmcli connection show
NAME                   UUID                                  TYPE       DEVICE 
br0                    47d3351f-a425-4cc5-a7d6-08469c7c280c  bridge     br0    
Wired connection 1     79066486-e007-4326-b22b-d9c3acecf2b9  ethernet   enp2s0 
bridge-slave-enp2s0    eeabbb24-b7f7-47e6-be6e-2aa7c1251e74  ethernet   --    
```

Bringing the bridge back up does not change a whole lot:

``` {bash}
markus@machine:~$ sudo nmcli con up br0
Connection successfully activated (master waiting for slaves) (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/3)
markus@machine:~$ nmcli connection show
NAME                 UUID                                  TYPE       DEVICE 
br0                  47d3351f-a425-4cc5-a7d6-08469c7c280c  bridge     br0    
Wired connection 1   79066486-e007-4326-b22b-d9c3acecf2b9  ethernet   enp2s0 
bridge-slave-enp2s0  eeabbb24-b7f7-47e6-be6e-2aa7c1251e74  ethernet   --     
```

Taking the "Wired connection 1" down brings things back to operation which was confirmed by taking a few Vagrant
boxes up and connected to the router:

``` {bash}
markus@machine:~$ sudo nmcli con down 'Wired connection 1'
Connection 'Wired connection 1' successfully deactivated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/2)
markus@machine:~$ nmcli connection show
NAME                 UUID                                  TYPE       DEVICE 
br0                  47d3351f-a425-4cc5-a7d6-08469c7c280c  bridge     br0    
bridge-slave-enp2s0  eeabbb24-b7f7-47e6-be6e-2aa7c1251e74  ethernet   enp2s0
Wired connection 1   79066486-e007-4326-b22b-d9c3acecf2b9  ethernet   --    
```

The article above suggests to remove the "Wired connection 1" altogether using

``` {bash}
markus@machine:~$ sudo nmcli connection delete 'Wired connection 1'
```

but I will keep it like this - for now...

---

## Replacing the default gateway on a Vagrant guest

[source](https://www.cyberciti.biz/faq/howto-debian-ubutnu-set-default-gateway-ipaddress/), [issue](https://github.com/hashicorp/vagrant/issues/2389)  
I've been running into a configuration issue with my libvirt bridge interface on the host machine not being used as
the default gateway. Vagrant assigned the route via the private network which it uses for ssh
and provisioning as the primary route. Manually swithching the default as below solves the job for now.

``` {bash}
vagrant@myubuntu:~$ ip route
default via 192.168.121.1 dev eth0 proto dhcp src 192.168.121.10 metric 100 
192.168.121.0/24 dev eth0 proto kernel scope link src 192.168.121.10 
192.168.121.1 dev eth0 proto dhcp scope link src 192.168.121.10 metric 100 
192.168.178.0/24 dev eth1 proto kernel scope link src 192.168.178.130

vagrant@myubuntu:~$ sudo ip route add default via 192.168.178.1

vagrant@myubuntu:~$ ip route
default via 192.168.178.1 dev eth1 
default via 192.168.121.1 dev eth0 proto dhcp src 192.168.121.10 metric 100 
192.168.121.0/24 dev eth0 proto kernel scope link src 192.168.121.10 
192.168.121.1 dev eth0 proto dhcp scope link src 192.168.121.10 metric 100 
192.168.178.0/24 dev eth1 proto kernel scope link src 192.168.178.130

vagrant@myubuntu:~$ sudo ip route del default via 192.168.121.1

vagrant@myubuntu:~$ ip route
default via 192.168.178.1 dev eth1 
192.168.121.0/24 dev eth0 proto kernel scope link src 192.168.121.10 
192.168.121.1 dev eth0 proto dhcp scope link src 192.168.121.10 metric 100 
192.168.178.0/24 dev eth1 proto kernel scope link src 192.168.178.130
```

---
