# Steps to secure a Linux Plesk server

* [Ban IP addresses and networks](#ban-ip-addresses-and-networks)
* [Configure FTP passive ports](#configure-ftp-passive-ports)

## Ban IP addresses and networks
### Automatically ban IP addresses and networks that generate malicious traffic

## Configure FTP passive ports
### configure a passive ports range for ProFTPd

1. Connect to the server thru SSH
2. Edit the /etc/proftpd.conf file inserting the following line inside the `Global` section
```bash
sudo vi /etc/proftpd.conf
```    
```
<Global>
...
PassivePorts 30000 31000
</Global>
```
3. If not already installed, install Plesk Firewall 
```bash
sudo plesk installer --select-release-current --install-component psa-firewall
```
4. If not already enabled, enable Plesk Firewall `Tools & Settings > Firewall` and click on the `Enable Firewall Rules Management` button, and then click on the `Enable` button.
5. Once changes are applied, click on the `Modify Plesk Firewall Rules` button and then on the `Add Custom Rule` one.
6. Specify the following information in the web form:
  * Name of the rule: **FTP Passive Ports**
  * Match direction: **Incoming**
  * Action: **Allow**
  * Add port or port range: set passive ports range specified in /etc/proftpd.conf, for example **30000-31000** and leave the **TCP option selected**, then click the `Add` button
  * Click `OK`
7. Click on the `Apply Changes` button
8. Click on the `Activate` button
9. Test your configuration

Plesk reference document: [213902285](https://support.plesk.com/hc/en-us/articles/213902285)
