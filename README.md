# Jarkom-Modul-5-B05-2021

## A. Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan Luffy dibawah ini:
![image](https://user-images.githubusercontent.com/71221969/145136562-46eca95e-7ad1-46a8-bc10-2b7db55299d2.png)
Keterangan : 	
- Doriki adalah DNS Server
- Jipangu adalah DHCP Server
- Maingate dan Jorge adalah Web Server
- Jumlah Host pada Blueno adalah 100 host
- Jumlah Host pada Cipher adalah 700 host
- Jumlah Host pada Elena adalah 300 host
- Jumlah Host pada Fukurou adalah 200 host

## B. Karena kalian telah belajar subnetting dan routing, Luffy ingin meminta kalian untuk membuat topologi tersebut menggunakan teknik CIDR atau VLSM. setelah melakukan subnetting
Dalam praktikum kali ini kami menggunakan metode CIDR:
- Step 1
![step1](https://user-images.githubusercontent.com/71221969/145153564-7be7c842-f136-4a1c-9a31-75e22bf07a8d.png)
- Step 2
![step2](https://user-images.githubusercontent.com/71221969/145153577-bb4cbbb7-16f6-427a-8112-46f78dfdbe98.png)
- Step 3
![step3](https://user-images.githubusercontent.com/71221969/145153586-8e050d1d-91a8-49b6-ac23-d5cca81ea5ec.png)
- Step 4
![step4](https://user-images.githubusercontent.com/71221969/145153600-7211bf6b-cf95-40b2-81de-99359cb3fa40.png)
- Step 5
![step5](https://user-images.githubusercontent.com/71221969/145153620-c4f25f97-1540-4860-b33f-d36d03022981.png)

  Kemudian dilakukan penghitungan:
  ![cidr](https://user-images.githubusercontent.com/71221969/145153657-554abe46-a24c-4ec7-9d1b-e8bf466e0bd8.png)

## C. Kalian juga diharuskan melakukan Routing agar setiap perangkat pada jaringan tersebut dapat terhubung.
Untuk melakukan routing, panggil command dibawah ini pada `Foosha` 
```
route add -net 192.179.0.0 netmask 255.255.240.0 gw 192.179.8.2
route add -net 192.179.16.0 netmask 255.255.248.0 gw 192.179.20.2
```
Dimana `192.179.0.0` adalah subnet milik D1 sedangkan `192.179.16.0` adalah subnet milik D2

## D. Tugas berikutnya adalah memberikan ip pada subnet Blueno, Cipher, Fukurou, dan Elena secara dinamis menggunakan bantuan DHCP server. Kemudian kalian ingat bahwa kalian harus setting DHCP Relay pada router yang menghubungkannya
- Langkah 1: Install dns server pada `Doriki`
```
apt-get update
apt-get install bind9 -y
```
- Langkah 2: Tambahkan konfigurasi pada `etc/bind/named.conf.option` dengan:
```
forwarders {
    8.8.8.8;
};
allow-query{any;};
```
![image](https://user-images.githubusercontent.com/71221969/145360837-3a004d8d-1733-413f-b1e6-8e1163faa990.png)


- Langkah 1: Install dhcp server pada `Jipangu`
  ```
  apt-get update
  apt-get install isc-dhcp-server
  ```
- Langkah 2: Ubah interfaces dari dhcp server ke `eth0` pada file `etc/default/isc-dhcp-server`
![image](https://user-images.githubusercontent.com/71221969/145342090-4a1e62a4-c0fc-4bc1-ae30-94c10af21478.png)

- langkah 3: Tambahkan file konfigurasi berikut pada `etc/dhcp/dhcpd.conf`:
```
subnet 192.179.4.128 netmask 255.255.255.248 {
}

subnet 192.179.4.0 netmask 255.255.255.128 {
        range 192.179.4.2 192.179.4.126;
        option routers 192.179.4.1;
        option broadcast-address 192.179.4.127;
        option domain-name-servers 192.179.4.131;
        default-lease-time 600;
        max-lease-time 600;
}

subnet 192.179.0.0 netmask 255.255.252.0 {
        range 192.179.0.2 192.179.3.254;
        option routers 192.179.0.1;
        option broadcast-address 192.179.3.255;
        option domain-name-servers 192.179.4.131;
        default-lease-time 600;
        max-lease-time 600;
}

subnet 192.179.16.0 netmask 255.255.254.0 {
        range 192.179.16.2 192.179.17.254;
        option routers 192.179.16.1;
        option broadcast-address 192.179.17.255;
        option domain-name-servers 192.179.4.131;
        default-lease-time 600;
}

subnet 192.179.18.0 netmask 255.255.255.0 {
        range 192.179.18.2 192.179.18.254;
        option routers 192.179.18.1;
        option broadcast-address 192.179.18.255;
        option domain-name-servers 192.207.4.131;
        default-lease-time 600;
        max-lease-time 600;
}
```
- Langkah 4: Install dhcp relay pada `Water7` dan `Guanhao` karena mereka sebagai router yang terhubung dengan dhcp client
```
apt-get update
apt-get install isc-dhcp-relay
```
setelah install tambahkan ip address dari dhcp server pada :
![image](https://user-images.githubusercontent.com/71221969/145346216-a2c3fdd4-15ba-4415-912d-77606bbe1b4b.png)

- Langkah 5: Kemudian pada network config pada DHCP client ditambahkan konfigurasi berikut:
```
auto eth0
iface eth0 inet dhcp
```
jangan lupa di restart, maka pada `etc/resolv.conf` akan auto terganti menjadi seperti ini:
![image](https://user-images.githubusercontent.com/71221969/145344950-5bb6eb69-24ac-4794-ac9e-6fd6d7a2bae3.png)
`192.179.4.131` adalah ip address dari dns server.
