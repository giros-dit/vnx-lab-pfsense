# VNX virtual scenario to test pfSense firewall

## Getting started
- Download and uncompress the tutorial scenario:
wget http://idefix.dit.upm.es/download/vnx/examples/pfsense/tutorial_pfsense-v02.tgz
sudo vnx --unpack tutorial_pfsense-v02.tgz 
cd tutorial_pfsense-v02/

- Optionally, change the pfSense firewall initial configuration:
```bash
cd conf/fw
./make-cfg-disk config.xml
cd ../..
```
- Start the scenario with:
```bash
sudo vnx -f tutorial_pfsense.xml --create
```
- Wait for the fw to complete startup (a *Bootup complete* message should be shown in the fw console #1).
- Start web servers in h3 and h4:
```bash
sudo vnx -f tutorial_pfsense.xml -x start-www
```
- The default configuration includes a rule to allow access to web servers running in h3 and h4 from h1 and h2. You can test that from h1 or h2 with:
```bash
curl 10.1.2.3
```
- Access pfSense fw configuration:
  - Connecting to http://10.1.2.1 from a web browser with credentials *admin/pfsense*
  - Connecting by SSH from the host to admin@10.1.2.1 with password *pfsense*

## Basic firewall configuration
The basic firewall configuration provided has been created in the flllowing way:
- Go to *Interfaces|WAN* and disable  Reserved networks options (block private and block bogon).
- Go to *Firewall|Aliases* and create an alias named *web_servers* including h3 (10.1.2.3) and h4 (10.1.2.4).
- Go to *Firewall|Rules|WAN* and create a rule with destination *web_servers* port HTTP(80) to allow access to web servers from Net0 y Net1.
- Go to *Firewall|Rules|LAN* and create a rule with destination *10.1.2.1* port HTTP(80) to allow access to management from Net2.

## Changing fw startup configuration
The initial configuration loaded by pfSense firewall is provided to the fw virtual machine through a virtual disk whose image is stored in *conf/fw* directory. To change the configuration, you have to create or modify a existing configuration file and copy it to the disk image.
There is a simple script in *conf/fw* directory to do that. If you execute:
```bash
cd conf/fw
./make-cfg-disk new-config.xml
```
the file *new-config.xml* will be copied to the disk image (data.img), changing the name to config.xml.
The next time the fw will start, it will load that configuration. You can restart the firewall with:
```bash
sudo vnx -f tutorial_pfsense.xml --destroy -M fw
sudo vnx -f tutorial_pfsense.xml --create -M fw
```
To modify a configuration the easiest way consist on:
- Starting the scenario and modifiying the configuration though the web interface
- Once modified, accessing fw by SSH, choosing option 8 and executing the following command to copy the configuration to the host:
```bash
scp /conf/config.xml user@10.1.2.10:     # change user to your username
```

