# Optimizing and Securing a Linux Plesk Server

* [Ban IP Addresses and Networks (Fail2ban)](#ban-ip-addresses-and-networks)
* [Web Application Firewall (ModSecurity)](#web-application-firewall)
* [Configure FTP passive ports (ProFTPd)](#configure-ftp-passive-ports)
* [Harden Nginx](#harden-nginx)
---
<br>

## Ban IP Addresses and Networks
#### [Plesk reference document (73381)](https://docs.plesk.com/en-US/onyx/administrator-guide/server-administration/protection-against-brute-force-attacks-fail2ban.73381/)

1. If not already installed, install Fail2ban
```
sudo plesk installer --select-release-current --install-component fail2ban
```
2. Go to `Tools & Settings` and then to `IP Address Banning (Fail2Ban)`
3. Click on the `Settings` tab and then select the `Enable intrusion detection` checkbox
4. Set the settings that suit your needs:
   - __IP address ban period__: time interval in seconds for which an IP address is banned. When this period is over, the IP address is automatically unbanned
   - __Time interval for detection of subsequent attacks__: time interval in seconds during which the system counts the number of unsuccessful login attempts and other unwanted actions from an IP address
   - __Number of failures before the IP address is banned__: number of failed login attempts from the IP address
5. Click on the `Apply` button
6. Click on the `Jails` tab
7. Select all the jails that you want to enable and then click on the `Switch On` button
8. Click on the `OK` button

<div align="right">

[:arrow_up:](#optimizing-and-securing-a-linux-plesk-server)

</div>  

## Web Application Firewall
#### [Plesk reference document (73383)](https://docs.plesk.com/en-US/onyx/administrator-guide/server-administration/web-application-firewall-modsecurity.73383/)

1. If not already installed, install ModSecurity
```
sudo plesk installer --select-release-current --install-component modsecurity
```
2. Go to `Tools & Settings` and then to `Web Application Firewall (ModSecurity)`
3. Click on the `General` tab
4. Select the `On` checkbox right to the `Web application firewall mode` section
5. Click on the `Settings` tab
6. Under the `Rule sets` section select the `Atomic Basic ModSecurity` radio button
7. Select the `Update rule sets` checkbox and define a `Daily` update in the select
8. Under the `Configuration` section select the `Tradeoff` radio button
9. Click on the `Apply` button
10. Click on the `OK` button

<div align="right">

[:arrow_up:](#optimizing-and-securing-a-linux-plesk-server)

</div>  

## Configure FTP passive ports
#### [Plesk reference document (213902285)](https://support.plesk.com/hc/en-us/articles/213902285)

1. Connect to the server thru SSH
2. Edit the __/etc/proftpd.conf__ file
```bash
sudo vi /etc/proftpd.conf
```
3. Insert the following line inside the `Global` section
```
<Global>
...
PassivePorts 30000 31000
</Global>
```
4. If not already installed, install Plesk Firewall 
```bash
sudo plesk installer --select-release-current --install-component psa-firewall
```
5. If not already enabled, enable Plesk Firewall `Tools & Settings > Firewall` and click on the `Enable Firewall Rules Management` button, and then click on the `Enable` button.
6. Once changes are applied, click on the `Modify Plesk Firewall Rules` button and then on the `Add Custom Rule` one.
7. Specify the following information in the web form:
   - Name of the rule: __FTP Passive Ports__
   - Match direction: __Incoming__
   - Action: __Allow__
   - Add port or port range: set passive ports range specified in /etc/proftpd.conf, for example __30000-31000__ and leave the __TCP option selected__, then click the `Add` button
   - Click on the `OK` button
8. Click on the `Apply Changes` button
9. Click on the `Activate` button
10. Test your configuration

<div align="right">

[:arrow_up:](#optimizing-and-securing-a-linux-plesk-server)

</div>  

## Harden Nginx
### Disable Server Tokens
1. Connect to the server thru SSH
2. Edit the __/etc/nginx/nginx.conf__ file
```bash
sudo vi /etc/nginx/nginx.conf
```
3. Insert the following line in the `http` section
```Nginx
http {
    ...
    # avoid sending Nginx version number in error pages and server header
    server_tokens off;
    ...
}
```
4. Save the file and test Nginx configuration
```bash
sudo nginx -t
```
5. Restart Nginx Web server for the changes to take effect
```bash
sudo systemctl restart nginx
```

### SSL/TLS Optimization
1. Connect to the server thru SSH
2. Edit the __/etc/nginx/conf.d/ssl.conf__ file
```bash
sudo vi /etc/nginx/conf.d/ssl.conf
```
3. Replace the content with the following lines
```Nginx
# Enable only secure cipher suites
ssl_ciphers EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH;
# Disable SSL 3, TLSv1, and TLSv1.1
ssl_protocols TLSv1.2 TLSv1.3;
# Server ciphers should be preferred over client ciphers when using TLS protocols
ssl_prefer_server_ciphers on;
# Enable session reuse to improve https performance
ssl_session_cache shared:SSL:60m;
ssl_session_timeout 1d;
ssl_session_tickets off;
```
3. Save the file and test Nginx configuration
```bash
sudo nginx -t
```
4. Restart Nginx Web server for the changes to take effect
```bash
sudo systemctl restart nginx
```

### Gzip Compression
1. Connect to the server thru SSH
2. Create the __gzip.conf__ file
```bash
sudo touch /etc/nginx/conf.d/gzip.conf
```
3. Insert the following lines
```Nginx
gzip on;
gzip_disable "MSIE [1-6]\\.(?!.*SV1)";
gzip_proxied any;
gzip_comp_level 5;
gzip_types text/plain text/css application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript image/x-icon image/bmp image/svg+xml;
gzip_vary on;
```

<div align="right">

[:arrow_up:](#optimizing-and-securing-a-linux-plesk-server)

</div>  

