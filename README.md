# VNX virtual scenario to test pfSense firewall


- Define the firewall initial configuration:
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
- Access pfSense fw config page by connecting to http://10.1.2.1 from a web browser with credentials *admin/pfsense*

## Restart firewall

```bash
sudo vnx -f tutorial_pfsense.xml --destroy -M fw
sudo vnx -f tutorial_pfsense.xml --create -M fw
```

## Basic firewall configuration
- Go to *Interfaces|WAN* and disable  Reserved networks options (block private and block bogon).
- Go to *Firewall|Aliases* and create an alias named *web_servers* including h3 (10.1.2.3) and h4 (10.1.2.4)
- Go to *Firewall|Rules|WAN* and create a rule with destination *web_servers* port HTTP(80)
- Go to *Firewall|Rules|LAN* and create a rule with destination *10.1.2.3* port HTTP(80)
