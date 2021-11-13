# Jarkom-Modul-3-B14-2021

## Topologi
![image](https://user-images.githubusercontent.com/45300477/141605090-0f80fb7f-5dd5-4a90-b527-befc27f53e2d.png)<br>
Konfigurasi:
- Foosha
```
auto eth0
iface eth0 inet dhcp

auto eth2
iface eth2 inet static
	address 10.14.2.1
	netmask 255.255.255.0
```
dan atur .bashrc dengan menambahkan `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.14.0.0/16` agar dapat mengakses jaringan luar (sesuai modul)
- EnniesLobby
```
auto eth0
iface eth0 inet static
	address 10.14.2.2
	netmask 255.255.255.0
	gateway 10.14.2.1
```
- Water7
```
auto eth0
iface eth0 inet static
	address 10.14.2.3
	netmask 255.255.255.0
	gateway 10.14.2.1
```
- Jipangu
```
auto eth0
iface eth0 inet static
	address 10.14.2.4
	netmask 255.255.255.0
	gateway 10.14.2.1
```
Dengan ketiganya telah diset
```
echo 'nameserver 192.168.122.1' > /etc/resolv.conf
```
pada .bashrc agar otomatis bisa menginstall yang diperlukan.

## 1. Mengatur EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server.


- EnniesLobby (DNS Server)
```
apt-get update
apt-get install bind9 -y
```
- Jipangu (DHCP Server)
```
apt-get update
apt-get install isc-dhcp-server -y
```
- Water7 (Proxy Server)
```
apt-get update
apt-get install squid -y
```

## 2. Foosha Sebagai DHCP Relay
Pertama install terlebih dahulu
```
apt-get update
apt-get install isc-dhcp-relay -y
```
dan atur agar server mengarah ke Jipangu sebagai DHCP Server dengan mengatur SERVERS ke ip Jipangu. Hal ini diatur agar saat baru dinyalakan dapat langsung ditekan enter karena setting akan otomatis disesuaikan.
```
echo ' 
SERVERS="10.14.2.4"

INTERFACES="eth1 eth2 eth3"

OPTIONS="" ' > /etc/default/isc-dhcp-relay
echo 'net.ipv4.ip_forward=1'>/etc/sysctl.conf
```

## Semua cliet HARUS menggunakan konfigurasi IP dari DHCP Server
Atur semua konfigurasi dari klien (Loguetown, Alabasta, Tottoland, Skypie) menjadi
```
auto eth0
iface eth0 inet dhcp
```
![image](https://user-images.githubusercontent.com/45300477/141609024-8d2d70ff-3510-4256-a13c-ef0c3c29052f.png)
![image](https://user-images.githubusercontent.com/45300477/141609038-6ff78e56-5cf0-46c5-9c2e-e085d9e83100.png)
![image](https://user-images.githubusercontent.com/45300477/141609048-f07cce9d-fdac-4549-a80d-2a82586ebb35.png)
![image](https://user-images.githubusercontent.com/45300477/141609051-863a6849-d734-471a-a84e-5eab2a90a603.png)

## 3. Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169
Hal ini dapat mengatur subnet 10.14.1.0 di `/etc/dhcp/dhcpd.conf` dengan
```
echo 'subnet 10.14.1.0 netmask 255.255.255.0 {
    range 10.14.1.20 10.14.1.99;
    range 10.14.1.150 10.14.1.169;
    option routers 10.14.1.1;
    option broadcast-address 10.14.1.255;
    option domain-name-servers 10.14.2.2 , 192.168.122.1;
    default-lease-time 360;
    max-lease-time 7200;
}
```
IP untuk Loguetown:<br>
![image](https://user-images.githubusercontent.com/45300477/141609168-2f1aee3f-b228-49b4-b80c-6eecbe50ddd5.png)<br>
IP untuk Alabasta:<br>
![image](https://user-images.githubusercontent.com/45300477/141609154-7fa99857-92ce-407a-9a67-0d5cb314ebfb.png)<br>

## 4. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50
Atur subnet 10.14.3.0 di `/etc/dhcp/dhcpd.conf` dengan
```
subnet 10.14.3.0 netmask 255.255.255.0 {
    range 10.14.3.30 10.14.3.50;
    option routers 10.14.3.1;
    option broadcast-address 10.14.3.255;
    option domain-name-servers 10.14.2.2 , 192.168.122.1;
    default-lease-time 720;
    max-lease-time 7200;
}
```

## 5. Klien mendapat DNS dari EnniesLobby dan terhubung dengan internet
Sudah bisa juga karena Loguetown sudah menginstall package dan melakukan update<br>
![image](https://user-images.githubusercontent.com/45300477/141609421-b34d9d3d-9eb3-4cc7-a8da-3ab77df81cb7.png)
![image](https://user-images.githubusercontent.com/45300477/141609428-0366e8ed-8251-4eb2-8d59-ca63ec9d4c4e.png)

## 6. Lama waktu DHCP Server pada switch 1 adalah 6 menit sedangkan 12 menit untuk switch 3
![image](https://user-images.githubusercontent.com/45300477/141609472-043afdf7-e506-4b05-bb95-5b06151ccef5.png)

## 7. Skypie memiliki alamat IP yang tetap
Setting pada Jipangu:<br>
![image](https://user-images.githubusercontent.com/45300477/141609502-5c8bedec-c744-4a7e-b15e-4ab6368263cb.png)<br>
IP Skypie:<br>
![image](https://user-images.githubusercontent.com/45300477/141609514-8da96ce6-c069-495b-beea-83c8f4ac8248.png)<br>
Konfig:<br>
![image](https://user-images.githubusercontent.com/45300477/141609532-567dc0ae-f635-4a3c-9d66-de5a9d690a6d.png)

## 8. Loguetown digunakan sebagai client Proxy agar transaksi jual beli dapat terjamin keamanannya, juga untuk mencegah kebocoran data transaksi.
![image](https://user-images.githubusercontent.com/45300477/141609655-93d9c388-b7e2-4690-9c3b-851740451a0e.png)
![image](https://user-images.githubusercontent.com/45300477/141609672-254055d9-1d8d-4b7b-ad78-826144cfce47.png)
![image](https://user-images.githubusercontent.com/45300477/141609720-356f6bb0-996a-4326-80e1-69cf95fe07b5.png)
![image](https://user-images.githubusercontent.com/45300477/141609815-221422c9-8ac5-4606-8c13-4894eae45326.png)

## 9. Menggunakan enkripsi MD5
![image](https://user-images.githubusercontent.com/45300477/141609883-8cfeba3c-8787-4ef5-af48-4667c46f597f.png)
![image](https://user-images.githubusercontent.com/45300477/141609920-8d3bcdd6-578b-4b5a-90ba-784480a279fa.png)

Login<br>
![image](https://user-images.githubusercontent.com/45300477/141611431-86f7a98b-e0c6-4e3c-ac3e-3ea335360646.png)
![image](https://user-images.githubusercontent.com/45300477/141611460-18ffbce9-5730-4ca6-a2c6-44f52a45738f.png)


## 10. Transaksi tidak dilakukan setiap hari
![image](https://user-images.githubusercontent.com/45300477/141609974-514d30e0-7e98-440b-8e77-cfe7fdd0b596.png)

Jika waktu sesuai (mengakses google.com):<br>
![image](https://user-images.githubusercontent.com/45300477/141611589-c99b63e1-700a-48af-ba0b-39c601f7b0d6.png)

Jika dilakukan di luar waktu yang ditentukan:<br>
![image](https://user-images.githubusercontent.com/45300477/141611455-1163fb2d-c8af-434d-9e96-27707a0f0815.png)




