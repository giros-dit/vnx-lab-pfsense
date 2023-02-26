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



```bash
```
