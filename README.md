# Zabbix nginx monitoring
Zabbix 4 Nginx monitoring by Alireza Zabihi based on Alex Gluck solution on Linux.

Tested on zabbix 4.4 

###### Connection Statistics:
- Active per second
- Reading
- Waiting per second
- Writing
- Requests pro connection

###### Request Statistics:
- Accepted per second
- Handled per second
- Total

###### Linux and Nginx performance monitoring:
- File descriptors (Maximum and Current)
- CPU Cores
- Worker_Connections
- Worker_Processes
- Maximum supported connection depended on configuration
- Nginx service status

###### Graph and screen:
- Requests Statistics graph
- File descriptors graph
- Read/Write Connections graph
- System Statistics graph

###### Triggers:
- Nginx service status
- Worker processes bad configuration

# Installation for RHEL8 (CENTOS8)
Installation is similar for other linux distribution

## Part 1 (Nginx side)

**1, Check for with-http_stub_status_module enabled or not**
```
nginx -V 2>&1 | grep -o with-http_stub_status_module 
```
Required output:
```
with-http_stub_status_module
```
**NOTE:**

RHEL8 is OK, 
if there is not any output you need to configure nginx with --with-http_stub_status_module:
```
./cofigure --with-http_stub_status_module
make && make install
```

**2, Edit Nginx configuration and add nginx_status location to your website server context** 

check "include" in /etc/nginx/nginx.conf in server http section
```
server {
    listen  80;
    ....
    include /etc/nginx/default.d/*.conf;
    ....
```
add localhost nginx_status for nginx monitoring 
```
vi /etc/nginx/default.d/stats.conf
  # add nginx_status location for nginx monitoring 
  location /nginx_status {
   	stub_status on;     
    access_log   off;     
    allow 127.0.0.1;
    deny all; }
```

**Restart nginx service** 
```
systemctl restart nginx
```

## Part 2 (Zabbix agent side)

You need to install the zabbix-agent or zabbix-agent2

**1, download script file "nginx.sh" and put it in nginx server in /etc/zabbix/** 
```
chmod 775 nginx.sh 
cp nginx.sh /etc/zabbix/
```

**2, Installing bc (Bash Calculator) if doesnt exist**
```
yum install bc -y
```

**3, Settings zabbix agent**

create file and add line

vi /etc/zabbix/zabbix_agent2.d/nginx.conf for zabbix_agent2

or

vi /etc/zabbix/zabbix_agentd.d/nginx.conf for zabbix_agent

```
UserParameter=nginx[*],/etc/zabbix/nginx.sh $1
```

**4, Restart zabbix-agent service**
```
systemctl restart zabbix-agent2
or
systemctl restart zabbix-agent
```

## Part 3 (Zabbix server side)

1, Download nginx_template.xml template and import it to your zabbix server from ---> Configuration -> Templates -> Import

2, Link imported template to your Nginx host

