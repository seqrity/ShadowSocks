## Pre Installation

### Change Server's Password

<code>passwd root</code>

### OS Update

<code>yum update && yum upgrade -y</code>



### Optional

<code>yum install epel-release -y</code>  
<code>yum install htop -y</code>

    
<br><br>

## Install Sahdowsocks

<code>yum install m2crypto python-setuptools -y</code>

<code>easy_install pip</code>

<code>pip install shadowsocks</code>

* * *

### Configuration

<code>yum install vim</code>

<code>vim /etc/shadowsocks.json</code>

<pre><code>
{
"server":"YOUR_SERVER_IP",
<br>"server_port":8000,
"local_port":1080,
"password":"YOUR_PASSWORD",
"timeout":600,
"method":"aes-256-cfb"
}
</code></pre>


### Add ShadowSocksss User
<code> adduser --system --no-create-home -s /bin/false shadowsocksss </code>


### Create Stratup Service

##### Create shadowsocks.service File
<code> vim /etc/systemd/system/shadowsocks.service </code>

<pre><code>
[Unit]
Description=ShadowsocksProxy
After=network.target

[Service]
User=shadowsocksss
Type=simple
ExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json  -v start
Restart=on-abort

[Install]
WantedBy=multi-user.target

</code></pre>


### Enable and start shadowsocks service

<pre><code>
systemctl daemon-reload
systemctl start shadowsocks
systemctl enable shadowsocks
</code></pre>



### Install & Configuration (Iptables)

<pre><code>
yum install iptables-services -y
iptables -I INPUT -p tcp --dport 8000 -j ACCEPT
systemctl enable iptables   
service iptables save
</code></pre>

##
If iptables rule doesn't save disable firewalld by the following command:

<pre><code>
systemctl disable firewalld
</code></pre>


### Logs
<code>less /var/log/shadowsocks.log </code>



### References:

https://www.linuxbabe.com/linux-server/setup-your-own-shadowsocks-server-on-debian-ubuntu-centos
https://www.linode.com/docs/networking/vpn/create-a-socks5-proxy-server-with-shadowsocks-on-ubuntu-and-centos7/
    

* * *



## Hardening (Optional)

### Install & Config fail2ban 

<code> yum install fail2ban </code>

#### Enable
<code> systemctl enable fail2ban </code>

#### Configuration

## ⛔ Don't edit jail.conf ⛔


#### Create jail.local

👉 Any values defined in jail.local will override those in jail.conf.


<code>vim /etc/fail2ban/jail.local</code>
<pre><code>

[DEFAULT]
# Ban hosts permanent:
maxretry = 3
bantime = -1
# Override /etc/fail2ban/jail.d/00-firewalld.conf:
banaction = iptables-multiport
[sshd]
enabled = true

</pre></code>

<code> systemctl restart fail2ban </code>

#### Check Status
<pre><code>
fail2ban-client status
fail2ban-client status sshd
iptables -L
</pre></code>

#### Create Fail2Ban Service

<code> vim /etc/systemd/system/myfail2ban.service </code>

<pre><code>
[Unit]
Description=Fail2BanService
After=network.target

[Service]
User=root
Type=simple
ExecStart=/bin/systemctl start fail2ban
Restart=on-abort

[Install]
WantedBy=multi-user.target

</pre></code>


### Enable & Start Service
<pre><code>
systemctl daemon-reload
systemctl start fail2ban
systemctl enable fail2ban
</pre></code>

### Clear Fail2Ban log file
<code>service fail2ban stop</code>

<code>truncate -s 0 /var/log/fail2ban.log</code>

<code>rm /var/lib/fail2ban/fail2ban.sqlite3</code>

<code>service fail2ban restart</code>

**You can save all of them in a .sh file and run it from crontab weekly(Sunday at 4:05AM)**

<code>5 4 * * sun /root/clear_fail2ban_log.sh</code>

### Reference:
https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-centos-7

* * *

## Install Client on Windows/Linux/Android

### Windows Client: https://github.com/shadowsocks/shadowsocks-windows/releases

<img src="win_client.png">

### Linux Client : https://github.com/shadowsocks/shadowsocks-qt5/releases

After download file run the following command:

<code>chmod a+x Shadowsocks-Qt5-x86_64.AppImage</code>


<img src="linux_client.png">


### Android Client: https://play.google.com/store/apps/details?id=com.github.shadowsocks

### Reference:
https://shadowsocks.org/en/download/clients.html
