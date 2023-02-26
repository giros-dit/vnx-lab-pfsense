# How to create a pfSense root filesystem for VNX

## References:

- https://kifarunix.com/install-pfsense-firewall-on-kvm/
- https://www.eve-ng.net/index.php/documentation/howtos/howto-add-pfsense-fw-2-4/

## Recipe

- Create the filesystem disk image:
```bash
qemu-img create -f qcow2 vnx_rootfs_kvm_pfsense-2.6.0.qcow2 10G
```
- Dowload pfsense ISO installer from https://pfsense.org/download and uncompress it:
```bash
gunzip pfSense-CE-2.6.0-RELEASE-amd64.iso.gz
```
- Install pfSense in the image:
```bash
virt-install --virt-type kvm --name pfsense --ram 2048 --vcpus 2 --cdrom=/pfSense-CE-2.6.0-RELEASE-amd64.iso --disk vnx_rootfs_kvm_pfsense-2.6.0.qcow2,bus=virtio,size=10,format=qcow2 --network default --network bridge=virbr0 --graphics vnc,listen=0.0.0.0 --noautoconsole --os-type=linux --os-variant=freebsd12.3 &
virt-viewer pfsense
```
- Open a shell and enable serial console:
```bash
echo "-Dh" > /boot.config
echo 'boot_multicons="YES"' >> /boot/loader.conf
echo 'boot_serial="YES"' >> /boot/loader.conf
echo 'console="comconsole,vidconsole"' >> /boot/loader.conf
```
- Stop the virtual machine and restart it with:
```bash
vnx --modify-rootfs vnx_rootfs_kvm_pfsense.qcow2 --mem 4G --arch x86_64 --vcpu 4 --update-aced 
```
- Access the virtual machine usiang *virsh console ...*.
- Exit configuration assistant with ctrl^c and exit to a shell to continue configuration.
- Enable access to external repositories:
  1. Make sure "enabled" key is set to "yes" in /etc/pkg/FreeBSD.conf
  2. Change "enabled: no" to "enabled: yes" in /usr/local/etc/pkg/repos/FreeBSD.conf
  3. Change "enabled: no" to "enabled: yes" in /usr/local/etc/pkg/repos/pfSense.conf for FreeBSD repo
- Configure re0 network interface:
```bash
dhclient re0
```
- Install additional packages:
```bash
pkg install bash
pw usermod root -s /usr/local/bin/bash
```
- Reboot to use serial console:
```bash
reboot
```
- Enable ssh from the pfSense GUI or editing /conf/config.xml file and adding this before the </system> closing tag:
```bash
   <ssh>
       <enable>enabled</enable>
   </ssh>
```
- Prepare the configuration to be loaded from a external disk:
```bash 
mkdir /data
cd /conf
mv config.xml /data/
ln -s /data/config.xml .
echo "/dev/da0                /data   msdosfs rw      2       2" >> /etc/fstab
```
- Stop the vm
```bash
halt -p
```

<!-- Don't install vnxaced as pfSense does not use the standard way of configuring network insterfaces
pw usermod vnx -s /usr/local/bin/bash 
pkg install perl5 p5-XML-LibXML p5-NetAddr-IP

- Install vnxace:
mount -t msdosfs /dev/vtbd1 /mnt/
# edit /mnt/vnxaced-lf/install_vnxaced and comment "if ($res)" block
perl /mnt/vnxaced-lf/install_vnxaced
-->

## Configure pfSense for the first time 
To create the first configuration file (config.xml) for pfSense in a virtual scenario:
- Start the virtual scenario and wait for pfsense menu to appear (takes some time till timeouts occur)
- From the console, use options 1 and 2 to define interfaces and configure ip adresses
- Activate SSH in option 14
- Open a shell (option 8) and copy the /conf/config.xml file to the host
- Stop the scenario
- Copy the config.xml file to conf/r2 directory
- Create the data disk with this configuration:
```bash
./make-storage config.xml
```






--- OLD

Comandos:

- See disks in FreeBSD:
```bash
geom disk list
```

qemu-img create -f qcow2 pfsense.qcow2 10G

virt-install --virt-type kvm --name pfsense --ram 2048 --vcpus 2 --cdrom=pfSense-CE-2.6.0-RELEASE-amd64.iso --disk pfsense.qcow2,bus=virtio,size=10,format=qcow2 --network default --network bridge=virbr0 --graphics vnc,listen=0.0.0.0 --noautoconsole --os-type=linux --os-variant=freebsd10.0 

virt-install --debug --name=Firewall --memory=4096 --vcpus=2 --location=/almacen/iso/pfSense-CE-2.6.0-RELEASE-amd64.iso --disk=pfsense2.qcow2,device=disk,bus=virtio,size=10 --network bridge:virbr0  --os-type=linux  --nographics --extra-args='console=tty0 console=ttyS0,115200n8 serial'

virt-install --virt-type kvm --name pfsense --ram 2048 --vcpus 2 --cdrom=pfSense-CE-2.6.0-RELEASE-amd64.iso --disk pfsense.qcow2,bus=virtio,size=10,format=qcow2 --network default --network bridge=virbr0 --noautoconsole --os-type=linux --os-variant=freebsd10.0 --nographics --extra-args='console=tty0 console=ttyS0,115200n8 serial'







1. Make sure "enabled" key is set to "yes" in /etc/pkg/FreeBSD.conf
2. Change "enabled: no" to "enabled: yes" in /usr/local/etc/pkg/repos/FreeBSD.conf
3. Change "enabled: no" to "enabled: yes" in /usr/local/etc/pkg/repos/pfSense.conf for FreeBSD repo
4. Now you're able to install packages from FreeBSD official repo



