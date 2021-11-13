# Jarkom-Modul-3-B14-2021

## 1. Topologi
Membuat (peta) topologi dengan kriteria EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server.
![image](https://user-images.githubusercontent.com/45300477/141605090-0f80fb7f-5dd5-4a90-b527-befc27f53e2d.png)

### Foosha
#### Sebagai router, konfigurasinya diatur dengan
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.14.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.14.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.14.3.1
	netmask 255.255.255.0
```
#### Sebagai DHCP Relay perlu untuk menginstallnya dengan
```
apt-get update
apt-get install isc-dhcp-relay -y
```
