3G-modem-on-BBB
===============

#install and configure ZTE 3G modem(MF637U) on Beaglebone black(Ubunut 13.04)

## install packages

```
apt-get update
apt-get install ppp wvdial
```

## Connect the Modem to BBB and then power on, find out the device

```
root@arm:~/3G_modem# lsusb 
Bus 001 Device 003: ID 19d2:0031 ZTE WCDMA Technologies MSM MF110/MF627/MF636
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

```
dmesg
[  767.847609] option1 ttyUSB2: GSM modem (1-port) converter now disconnected from ttyUSB2
```

## Configure wvdial.conf

```
cat /etc/wvdial.conf
[Dialer Defaults]
Phone = *99#
Username = { }
Password = { }
Stupid Mode = 1
Dial Command = ATDT


[Dialer 3g]
Init1 = ATZ
Init2 = ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0
;Using a lebera sim card shoud set APN to purtona.net, change it to yours.
Init3 = AT+CGDCONT=1,"IP","purtona.net"
Stupid Mode = 1
Modem Type = Analog Modem
ISDN = 0
New PPPD = yes
Modem = /dev/ttyUSB2
Username = " "
Password = " "
Baud = 9600
```

## Dial up
```
root@arm:/etc# wvdial 3g
--> WvDial: Internet dialer version 1.61
--> Initializing modem.
--> Sending: ATZ
ATZ
OK
--> Sending: ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0
ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0
OK
--> Sending: AT+CGDCONT=1,"IP","purtona.net"
AT+CGDCONT=1,"IP","purtona.net"
OK
--> Modem initialized.
--> Sending: ATDT*99#
--> Waiting for carrier.
ATDT*99#
CONNECT 7200000
--> Carrier detected.  Starting PPP immediately.
--> Starting pppd at Wed Jun  4 07:05:04 2014
--> Pid of pppd: 1489
--> Using interface ppp0
--> pppd: ��[02]
--> pppd: ��[02]
--> pppd: ��[02]
--> pppd: ��[02]
--> pppd: ��[02]
--> pppd: ��[02]
--> local  IP address 101.113.10.91
--> pppd: ��[02]
--> remote IP address 10.64.64.64
--> pppd: ��[02]
--> primary   DNS address 10.143.147.147
--> pppd: ��[02]
--> secondary DNS address 10.143.147.148
--> pppd: ��[02]
```

## Check the connection
```
root@arm:~# ifconfig ppp0
ppp0      Link encap:Point-to-Point Protocol  
          inet addr:101.113.8.201  P-t-P:10.64.64.64  Mask:255.255.255.255
          UP POINTOPOINT RUNNING NOARP MULTICAST  MTU:1500  Metric:1
          RX packets:10 errors:0 dropped:0 overruns:0 frame:0
          TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:3 
          RX bytes:711 (711.0 B)  TX bytes:323 (323.0 B)

root@arm:~# ping www.google.com
PING www.google.com (74.125.237.144) 56(84) bytes of data.
64 bytes from syd01s13-in-f16.1e100.net (74.125.237.144): icmp_req=1 ttl=56 time=666 ms
64 bytes from syd01s13-in-f16.1e100.net (74.125.237.144): icmp_req=2 ttl=56 time=80.2 ms
64 bytes from syd01s13-in-f16.1e100.net (74.125.237.144): icmp_req=3 ttl=56 time=64.7 ms
```
