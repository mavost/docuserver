# Ubuntu tips and tricks

## Resizing partititons

Use case: when a virtual machine's guest runs out of space

1. extend the associated volume on the host system,
2. from a running Ubuntu guest system, get an impression of the current state of the partitioning:  
`sudo parted /dev/sda 'unit s print' free`
3. extend the partition table using:  
`sudo cparted` using the steps resizing and writing the table.
4. extend the actual file system:  
`sudo resize2fs /dev/sda3 10353915`  
the number is provided in blocks (4096B) and based on the sector length (512B).

Note: this might require additional steps for more complicated partitioning and is generally a bad idea for active systems.

## Fix busted networking

Use case: a virtual machine has collapsed and its network is no longer reachable or able to ping outside

Try one of the following:

- on the guest settings of your virtual machine, remove the networking adapter and reinsert it,
- `sudo nmcli networking on`,
- `sudo apt-get --reinstall install docker-ce docker-ce-cli containerd.io`,
