# How to create a pfSense root filesystem for VNX

## References:

- Dowload pfsense. pfsense.org/download
- https://kifarunix.com/install-pfsense-firewall-on-kvm/
- https://www.eve-ng.net/index.php/documentation/howtos/howto-add-pfsense-fw-2-4/

## Recipe

Create the filesystem disk image:
```bash
qemu-img create -f qcow2 vnx_rootfs_kvm_pfsense.qcow2 10G
```

virt-install --virt-type kvm --name pfsense --ram 2048 --vcpus 2 --cdrom=/almacen/iso/pfSense-CE-2.6.0-RELEASE-amd64.iso --disk vnx_rootfs_kvm_pfsense.qcow2,bus=virtio,size=10,format=qcow2 --network default --network bridge=virbr0 --graphics vnc,listen=0.0.0.0 --noautoconsole --os-type=linux --os-variant=freebsd10.0

virt-viewer pfsense

vnx --modify-rootfs vnx_rootfs_kvm_pfsense.qcow2 --mem 4G --arch x86_64 --vcpu 4 --update-aced 
- Enable access to external repositories:
  1. Make sure "enabled" key is set to "yes" in /etc/pkg/FreeBSD.conf
  2. Change "enabled: no" to "enabled: yes" in /usr/local/etc/pkg/repos/FreeBSD.conf
  3. Change "enabled: no" to "enabled: yes" in /usr/local/etc/pkg/repos/pfSense.conf for FreeBSD repo

- Configure networ interface:
dhclient re0

- Install packages:
pkg install bash
pw usermod root -s /usr/local/bin/bash
pw usermod vnx -s /usr/local/bin/bash 
pkg install perl5 p5-XML-LibXML p5-NetAddr-IP

- Install vnxace:
mount -t msdosfs /dev/vtbd1 /mnt/
# edit /mnt/vnxaced-lf/install_vnxaced and comment "if ($res)" block
perl /mnt/vnxaced-lf/install_vnxaced

- Enable serial console:
echo "-Dh" > /boot.config
echo 'boot_multicons="YES"' >> /boot/loader.conf
echo 'boot_serial="YES"' >> /boot/loader.conf
echo 'console="comconsole,vidconsole"' >> /boot/loader.conf

- Reboot to use serial console:
reboot

- Enable ssh from the pfSense GUI

- Stop the vm


To create the config.xml pfSense configuration file for a specific scenario:

- Start the virtual escenario 
- From the console, use the menus to configure the interfaces
- Copy the /conf/config.xml file to the host
- Stop the scenario
- Start the pfSense image with "vnx --modify-rootfs"
- Copy the config.xml file to /conf
- Stop the vm

- Start the scenario 




- See disks in FreeBSD:

geom disk list

vi /etc/fstab
/dev/da0                data    msdosfs rw      2       2


--- OLD

Comandos:

qemu-img create -f qcow2 pfsense.qcow2 10G

virt-install --virt-type kvm --name pfsense --ram 2048 --vcpus 2 --cdrom=pfSense-CE-2.6.0-RELEASE-amd64.iso --disk pfsense.qcow2,bus=virtio,size=10,format=qcow2 --network default --network bridge=virbr0 --graphics vnc,listen=0.0.0.0 --noautoconsole --os-type=linux --os-variant=freebsd10.0 

virt-install --debug --name=Firewall --memory=4096 --vcpus=2 --location=/almacen/iso/pfSense-CE-2.6.0-RELEASE-amd64.iso --disk=pfsense2.qcow2,device=disk,bus=virtio,size=10 --network bridge:virbr0  --os-type=linux  --nographics --extra-args='console=tty0 console=ttyS0,115200n8 serial'

virt-install --virt-type kvm --name pfsense --ram 2048 --vcpus 2 --cdrom=pfSense-CE-2.6.0-RELEASE-amd64.iso --disk pfsense.qcow2,bus=virtio,size=10,format=qcow2 --network default --network bridge=virbr0 --noautoconsole --os-type=linux --os-variant=freebsd10.0 --nographics --extra-args='console=tty0 console=ttyS0,115200n8 serial'







1. Make sure "enabled" key is set to "yes" in /etc/pkg/FreeBSD.conf
2. Change "enabled: no" to "enabled: yes" in /usr/local/etc/pkg/repos/FreeBSD.conf
3. Change "enabled: no" to "enabled: yes" in /usr/local/etc/pkg/repos/pfSense.conf for FreeBSD repo
4. Now you're able to install packages from FreeBSD official repo



