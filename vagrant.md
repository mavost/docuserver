# installing and using Vagrant and QEMU/KVM provider (libvirt)

## getting Vagrant from original repo
[setting up repository, ](https://superuser.com/questions/845987/how-do-i-upgrade-vagrant-to-the-latest-version-in-ubuntu/845989)  
[installation](https://ostechnix.com/how-to-use-vagrant-with-libvirt-kvm-provider/)  
```
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update
sudo apt-get -y install vagrant
```

---

## Installation of libvirt plugins on Ubuntu (Focal 20.04)
Existing issues between libs and dev packages (2021-08-15):
- libvirt must be 6.0.0-0ubuntu8.3 (not younger) to match libvirt-dev 
- zlib1g must be 1:1.2.11.dfsg-2ubuntu1 (not younger) to match zlib1g-dev

After fixing compatibility issues install the whole shebang of virtualization packages and plugins to enable libvirt provider in vagrant
1. `sudo apt list -a libvirt0`  
   `sudo apt list -a zlib1g`
2. `sudo apt-get update`  
   `sudo apt-get install libvirt0=6.0.0-0ubuntu8.3`  
   `sudo apt-get install zlib1g=1:1.2.11.dfsg-2ubuntu1`
3. `sudo apt install qemu libvirt-daemon-system libvirt-clients libxslt-dev libxml2-dev libvirt-dev zlib1g-dev ruby-dev ruby-libvirt ebtables dnsmasq-base`
4. `vagrant plugin install vagrant-libvirt`  
   `vagrant plugin install vagrant-mutate` &rightarrow; conversion tool for generic Vagrant boxes to be run with libvirt

---

## using Vagrant
running commands on shell in folder where IAC project (**Vagrantfile** etc.) resides or will reside
### verify that Vagrant works
`vagrant --help`
### initialize image from repository
[Repository of Vagrant Boxes](https://app.vagrantup.com/boxes/search?utf8=✓&sort=downloads)  
`vagrant init generic/alpine310`
### provision instance
`vagrant up --provider=libvirt`
### access instance
`vagrant ssh`
### stop instance
`vagrant suspend`
### restart instance
`vagrant resume`
### stopping and removing instance
`vagrant destroy`


