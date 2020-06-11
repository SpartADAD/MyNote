# 1.2　配置网络服务和安全通信

配置静态IP ,使配置永久保持，需要

编辑/etc/network/interfaces

```
auto wlan0
iface wlan0 inet static
address 192.168.0.128 
netmask 255.255.255.0
network 192.168.0.0
broadcast 192.168.0.255
gateway 192.168.0.1 
#(需要连接的wifi SSID)
wpa-ssid XXX
#（通过wpasupplicant生成无线上网的psk码)
wpa-psk XXXXX 
```

配置DNS，修改/etc/resolv.conf

```
nameserver 114.114.114.114
nameserver 8.8.8.8
```



## **注解**：

### 编辑/etc/network/interfaces

```
auto wlan0
iface wlan0 inet static
address 192.168.0.128 
netmask 255.255.255.0
network 192.168.0.0
broadcast 192.168.0.255
gateway 192.168.0.1 
```

需要注意的是如果在这里配置

```
dns-nameservers 114.114.114.114 8.8.8.8
```

浏览器能够上网，但是无法在命令行里ping通网站，似乎是由于/etc/resolv.conf配置DNS才会影响到命令行的上网，最好配置DNS还是在/etc/resolv.conf。



### 手动连接wifi配置

此时reboot后，会出现wifi图标消失，此时似乎没有wifi连接，这时候无法上网，需要在/etc/network/interfaces，加上手动连接wifi配置：

```
#(需要连接的wifi SSID)
wpa-ssid XXX
#（通过wpasupplicant生成无线上网的psk码)
wpa-psk XXXXX 
```

生成方法如下：

```
#扫描周围wifi
iwlist wlan0 scan

#只输出 SSID
iwlist wlan0 scan | grep ESSID

#根据无线上网的SSID和密码生成wlan相应的配置文件
wpa_passphrase 要连接的SSID 密码 > xxx.conf

#然后查看xxx.conf，其中有ssid和根据无线密码生成的psk码
cat xxx.conf
```



在配置完wifi后，reboot，wifi图标会消失，可以通过以下命令，手动进行wifi信息查看和连接

```
#查看当前连接wifi
iw dev
#检查无线网卡情况（假设无线网卡是wlan0）
iw dev wlan0 link
#开启无线网卡
ip link set wlan0 up
#连接wifi（根据essid连接）
iw wlan0 connect [essid]
#通过DHCP获取IP地址
dhclient wlan0
```



### **最终一定要配置DNS**

最需要注意的：

​						需要配置DNS 国内：114.114.114.114

​						国外：8.8.8.8

否则重启后：1)浏览器无法正常上网，虽然可以通过service networking restart解决，但是依旧会出现2)情况

​						2)命令行ping命令无法正确解析域名，ping通网站。例如ping www.baidu.com,无法正确解析百度域名；



------

​	**注意**：ubuntu使用了两条配置线路 /etc/network/interfaces 这个配置文件主要用于便于服务器版本的ubuntu系统使用；同时为了适应移动办公造成ip和网络环境不断变化，ubuntu在/NetworkManager/NetworkManager.conf 中进行了配置。这两个文件是冲突的，采取的策略是，当interfaces文件被修改时，以此文件上的配置为准，但如果/etc/NetworkManager/NetworkManager.conf 中managed参数为true，便会以NetworkManager.conf为准。

​	但在我个人做的实验中，在未加入手动连接wifi配置时，managed参数为true时，重启后，wifi图标出现，同时会恢复/etc/resolv.conf默认DNS配置。但是加入手动连接wifi配置后，重启，wifi图标消失，只能进行手动配置连接

------

