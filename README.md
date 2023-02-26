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
