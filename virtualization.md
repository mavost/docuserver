# QEMU/KVM

[source](https://help.ubuntu.com/community/KVM/Installation)

## install QEMU/KVM on Host

``` {bash}
sudo apt install -y bridge-utils
sudo apt install -y virtinst qemu-kvm libvirt-daemon-system libvirt-clients
sudo apt install -y virt-manager
```

## add user to relevant groups

1. run

   ``` {bash}
   sudo usermod -aG kvm $USER
   sudo usermod -aG libvirt $USER
   ```

2. on Ubuntu either logout and wait for at least 20s for re-login or better reboot to apply change

## install network bridge on Host

For virtualization use cases where server guests run in parallel to the Host we want the default NAT gateway to be replaced with a bridged mode, hence every guest can get its own IP with the router of the private network on which the Host runs. Ubuntu uses a mix of Netplan (yaml)/networkd and NetworkManager administration tools.  
[source1,](https://www.answertopia.com/ubuntu/creating-an-ubuntu-kvm-networked-bridge-interface)
 [source2](https://blog.buettner.xyz/kvm-ubuntu-20-04-installieren-bridged-networking-konfigurieren)

### show connections using NetworkManager CMD line tool

for configuration of the ethernet interface  
`nmcli con show`  
&rightarrow;

``` {bash}
NAME                UUID                                  TYPE      DEVICE
Wired connection 1  56f32c14-a4d2-32c8-9391-f51967efa173  ethernet  eno1
virbr0              59bf4111-e0d2-4e6c-b8d4-cb70fa6d695e  bridge    virbr0
```

### show VM networks

only the default NAT bridge available  
`virsh net-list --all`  
&rightarrow;

``` {bash}
 Name                 State      Autostart     Persistent
----------------------------------------------------------
 default              active     yes           yes
```

### show network devices

`nmcli device show`

### creating the bridge

1. add bridge connection  
`nmcli con add ifname br0 type bridge con-name br0`
2. establish interface  
`nmcli con add type bridge-slave ifname eno1 master br0`
3. reup connection  
`nmcli con down "Wired connection 1"`  
`nmcli con up br0`

### verify result

`nmcli con show`  
&rightarrow;

``` {bash}
NAME                UUID                                  TYPE      DEVICE 
br0                 8416607e-c6c1-4abb-8583-1661689b95a9  bridge    br0    
bridge-slave-eno1   43383092-6434-448f-b735-0cbea39eb38f  ethernet  eno1   
virbr0              dffab88d-1588-4e69-8d1c-2148090aa5ee  bridge    virbr0 
Wired connection 1  56f32c14-a4d2-32c8-9391-f51967efa173  ethernet  --
```

### connect QEMU/KVM to with the new network bridge

1. create `bridge.xml` file containing

   ``` {bash}
    <network>
      <name>br0</name>
      <forward mode="bridge"/>
      <bridge name="br0" />
    </network>
   ```

2. apply file  
`virsh net-define ./bridge.xml`  
3. start and autostart  
`virsh net-start br0`  
`virsh net-autostart br0`  
4. verify result
    `virsh net-list --all`  
    &rightarrow;

    ``` {bash}
    Name                 State      Autostart     Persistent
    =========================================================
    br0                  active     yes           yes
    default              active     yes           yes
    ```

5. during setup instruct new machines to use the new bridge interface

---

## Running VMware machine images on QEMU/KVM

- in case of thin-provisioned images, firstly convert the image to a monolithic flatfile  
    [VMware - Virtual Disk Manager - Manual](https://www.vmware.com/pdf/VirtualDiskManager.pdf)
    > -t [0|1|2|3|4|5] Specifies the virtual disk type. This option is required when you create or convert a virtual disk. Choose one of the following types:  
    > 0 – create a growable virtual disk contained in a single file (monolithic sparse).  
    > 1 – create a growable virtual disk split into 2GB files (split sparse).  
    > 2 – create a preallocated virtual disk contained in a single file (monolithic flat).  
    > 3 – create a preallocated virtual disk split into 2GB files (split flat).  
    > 4 – create a preallocated virtual disk compatible with ESX server (VMFS flat).  
    > 5 – create a compressed disk optimized for stream

    Example on **Windows** command line:  
    `vmware-vdiskmanager -r sourceDisk.vmdk -t 2 targetDisk.vmdk`  
    &rightarrow; creates flat data file and file descriptor which both need to be copied to Linux image storage, e.g. to `/var/lib/libvirt/images` by using a samba share mount

- convert monolithic vmdk image to qcow2 image

  ``` {bash}
  cd /var/lib/libvirt/images
  qemu-img convert -f vmdk -O qcow2 file_in.vmdk file_out.qcow2
  ```

- change ownership and cleanup

  ``` {bash}
  sudo chown libvirt-qemu:kvm file_out.qcow2
  rm file_in.vmdk
  ```

---

### edit virtual machine shutdown behavior

[source](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/virtualization_deployment_and_administration_guide/sect-shutting_down_rebooting_and_force_shutdown_of_a_guest_virtual_machine-manipulating_the_libvirt_guests_configuration_settings)  
`sudo nano /usr/lib/libvirt/libvirt-guests.sh`  
&rightarrow; apparently slightly bugged on Ubuntu 20.04 so settling for manual shutdown on exit

### list running machines

`virsh list --all`  
&rightarrow;

``` {bash}
Id   Name          State
------------------------------
 -    ubuntu20.04   shut off
 -    Win10pro      shut off
```

### starting machines

`virsh start ubuntu20.04`  
&rightarrow;  
Domain ubuntu20.04 started  
`virsh list --all`  
&rightarrow;

``` {bash}
Id   Name          State
------------------------------
 3    ubuntu20.04   running
 -    Win10pro      shut off
```

### stopping machine (gracefully)

`virsh start ubuntu20.04`  
&rightarrow;

``` {bash}
Domain ubuntu20.04 is being shutdown
```
