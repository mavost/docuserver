# installing Vagrant and QEMU/KVM provider

## getting Vagrant from newest source
[setting up repository, ](https://superuser.com/questions/845987/how-do-i-upgrade-vagrant-to-the-latest-version-in-ubuntu/845989)  
[installation](https://ostechnix.com/how-to-use-vagrant-with-libvirt-kvm-provider/)  
```
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update
sudo apt-get -y install vagrant
```

## getting QEMU/KVM plugins for Vagrant
`sudo apt install qemu libvirt-daemon-system libvirt-clients libxslt-dev libxml2-dev libvirt-dev zlib1g-dev ruby-dev ruby-libvirt ebtables dnsmasq-base`

### Fixing problems with compatibility between newest libvirt and Vagrant plugin requirements
&rightarrow; (2021-08-15) use 6.0.0-0ubuntu8.3 (not younger)

compatibility to dev-package
1. `sudo apt list -a libvirt0`  
   `sudo apt-get install libvirt0=6.0.0-0ubuntu8.3`
2. `sudo apt-get install libvirt-dev`
3. `sudo apt-get install libvirt-daemon-system`