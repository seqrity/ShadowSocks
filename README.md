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
</pre></code>


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
# Ban hosts for one hour:
bantime = 3600
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

### Reference:
https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-centos-7


