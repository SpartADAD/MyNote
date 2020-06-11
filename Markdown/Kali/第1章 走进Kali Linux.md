# 1.2　配置网络服务和安全通信

## 1.2.1

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



### **注解**：

#### 编辑/etc/network/interfaces

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



#### 手动连接wifi配置

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



#### **最终一定要配置DNS**

最需要注意的：

​						需要配置DNS 国内：114.114.114.114

​						国外：8.8.8.8

否则重启后：1)浏览器无法正常上网，虽然可以通过service networking restart解决，但是依旧会出现2)情况

​						2)命令行ping命令无法正确解析域名，ping通网站。例如ping www.baidu.com,无法正确解析百度域名；



------

​	**注意**：ubuntu使用了两条配置线路 /etc/network/interfaces 这个配置文件主要用于便于服务器版本的ubuntu系统使用；同时为了适应移动办公造成ip和网络环境不断变化，ubuntu在/NetworkManager/NetworkManager.conf 中进行了配置。这两个文件是冲突的，采取的策略是，当interfaces文件被修改时，以此文件上的配置为准，但如果/etc/NetworkManager/NetworkManager.conf 中managed参数为true，便会以NetworkManager.conf为准。

​	但在我个人做的实验中，在未加入手动连接wifi配置时，managed参数为true时，重启后，wifi图标出现，同时会恢复/etc/resolv.conf默认DNS配置。但是加入手动连接wifi配置后，重启，wifi图标消失，只能进行手动配置连接

------





## 1.2.2 使用安全Shell保护通信安全

​	理论上来说，每次安装服务器时SSH密钥（SSH Host Key）都是自动生成的，而生成出相同密钥的概率接近于0，这样避免了中间攻击等情况。但是，就是存在以下情况使得两台SSH密钥相同：

- 在虚拟化技术中克隆了一台虚拟机；
- 将原来的虚拟硬盘复制后新建虚拟机运行。

​	可能遇到碰到的VPS云服务器重装系统复制完模板数据后不重新生成SSH密钥的，于是整个云平台所有的VPS都跑着相同的SSH密钥，如果要实现中间攻击只需要新建一台云就能获得私钥了。

​	在测试过程中，为了最小化被目标网络检测到的可能，Kali不用任何外部监听网络服务。Kali用默认的SSH密钥进行预配置。在开启SSH服务前，禁用默认密钥，并生成一个唯一的密钥集以供使用。将默认的SSH密钥移动到备份文件夹，然后通过下列命令生成一个新的SSH密钥集：

```
dpkg-reconfigure openssh-server
mkdir keys_default
mv ssh_host_* keys_default
```

​	比较默认和新生成密钥的哈希值md5sum

```
md5sum ssh_host_*
cd keys_default
md5sum *
```

​	启动/终止SSH

```
/etc/init.d/ssh start
/etc/init.d/ssh stop
```

​	查看SSH是否在运行

```
netstat -antp
```

# 1.3　更新Kali Linux

​	Debian软件包管理系统：默认程序包资源库在目录/etc/apt/sources.list中，如果不存在，编辑sources.list文件，增加它们。dpkg是Debian的软件包管理系统，可以使用命令行来安装、移除和查询软件包。高级软件包工具（Advanced Packaging Tool，APT），通过搜索资源库、安装或者升级的软件包，以及所有需要的依赖性，来扩展dkpg的功能。APT也可以用来升级完整的版本。







# 1.4　配置和自定义Kali Linux

## 1.4.5　用TrueCrypt创建加密文件夹