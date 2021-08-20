# networking topics

## listing network interfaces
- **ubuntu**: 
    - `ip addr` &rightarrow; generally
    - `ip -4 a` &rightarrow; list the IP4 interfaces only
    ```
    $ ip -4 a  
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