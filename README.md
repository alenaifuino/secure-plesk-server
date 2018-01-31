# Steps to secure a Linux Plesk server

* [Ban IP addresses and networks (Fail2ban)](#ban-ip-addresses-and-networks)
* [Web Application Firewall (ModSecurity)](#web-application-firewall)
* [Configure FTP passive ports (ProFTPd)](#configure-ftp-passive-ports)


## Ban IP addresses and networks    [:arrow_up:](#steps-to-secure-a-linux-plesk-server)
#### [Plesk reference document](https://docs.plesk.com/en-US/onyx/administrator-guide/server-administration/protection-against-brute-force-attacks-fail2ban.73381/)

1. If not already installed, install Fail2ban
2. Go to `Tools & Settings` and then to `IP Address Banning (Fail2Ban)`
3. Click on the `Settings` tab and then select the `Enable intrusion detection` checkbox
4. Set the settings that suit your needs:
   - **IP address ban period**: time interval in seconds for which an IP address is banned. When this period is over, the IP address is automatically unbanned
   - **Time interval for detection of subsequent attacks**: time interval in seconds during which the system counts the number of unsuccessful login attempts and other unwanted actions from an IP address
   - **Number of failures before the IP address is banned**: number of failed login attempts from the IP address
5. Click on the `Apply` button
6. Click on the `Jails` tab
7. Select all the jails that you want to enable and then click on the `Switch On` button
8. Click on the `OK` button


## Web Application Firewall
#### [Plesk reference document](https://docs.plesk.com/en-US/onyx/administrator-guide/server-administration/web-application-firewall-modsecurity.73383/)

1. If not already installed, install ModSecurity
2. Go to `Tools & Settings` and then to `Web Application Firewall (ModSecurity)`
3. Click on the `General` tab
4. Select the `On` checkbox right to the `Web application firewall mode` section
5. Click on the `Settings` tab
6. Under the `Rule sets` section select the `Atomic Basic ModSecurity` radio button
7. Select the `Update rule sets` checkbox and define a `Daily` update in the select
8. Under the `Configuration` section select the `Tradeoff` radio button
9. Click on the `Apply` button
10. Click on the `OK` button


## Configure FTP passive ports
#### [Plesk reference document](https://support.plesk.com/hc/en-us/articles/213902285)

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
   - Name of the rule: **FTP Passive Ports**
   - Match direction: **Incoming**
   - Action: **Allow**
   - Add port or port range: set passive ports range specified in /etc/proftpd.conf, for example **30000-31000** and leave the **TCP option selected**, then click the `Add` button
   - Click on the `OK` button
7. Click on the `Apply Changes` button
8. Click on the `Activate` button
9. Test your configuration
