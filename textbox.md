# Textbox

``` {bash}
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update
sudo apt-get -y install vagrant

sudo apt install qemu libvirt-daemon-system libvirt-clients libxslt-dev libxml2-dev libvirt-dev zlib1g-dev ruby-dev ruby-libvirt ebtables dnsmasq-base
```

## Problems with compatibility between newest libvirt and Vagrant plugin requirements

--> use 6.0.0-0ubuntu8.3 (not younger)

compatibility to dev-package

``` {bash}
sudo apt list -a libvirt0  
sudo apt-get install libvirt0=6.0.0-0ubuntu8.3
sudo apt-get install libvirt-dev
sudo apt-get install libvirt-daemon-system
```
