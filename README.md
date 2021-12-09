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

Berikut langkah-langkah untuk menginstall dns server dan dhcp server:
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


- Langkah 3: Install dhcp server pada `Jipangu`
  ```
  apt-get update
  apt-get install isc-dhcp-server
  ```
- Langkah 4: Ubah interfaces dari dhcp server ke `eth0` pada file `etc/default/isc-dhcp-server`
![image](https://user-images.githubusercontent.com/71221969/145342090-4a1e62a4-c0fc-4bc1-ae30-94c10af21478.png)

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


- langkah 1: Tambahkan file konfigurasi berikut pada `etc/dhcp/dhcpd.conf`:
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
- Langkah 2: Install dhcp relay pada `Water7` dan `Guanhao` karena mereka sebagai router yang terhubung dengan dhcp client
```
apt-get update
apt-get install isc-dhcp-relay
```
setelah install tambahkan ip address dari dhcp server pada :
![image](https://user-images.githubusercontent.com/71221969/145346216-a2c3fdd4-15ba-4415-912d-77606bbe1b4b.png)

- Langkah 3: Kemudian pada network config pada DHCP client ditambahkan konfigurasi berikut:
```
auto eth0
iface eth0 inet dhcp
```
jangan lupa di restart, maka pada `etc/resolv.conf` akan auto terganti menjadi seperti ini:
![image](https://user-images.githubusercontent.com/71221969/145344950-5bb6eb69-24ac-4794-ac9e-6fd6d7a2bae3.png)
<br>
`192.179.4.131` adalah ip address dari dns server.

## Soal 1. Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.
Langkah 1: panggil command berikut pada `Foosha`
```
iptables -t nat -A POSTROUTING -s 192.179.0.0/16 -o eth0 -j SNAT --to-s 192.168.122.2
```
Langkah 2: testing pada topologi 
- Elena
![image](https://user-images.githubusercontent.com/71221969/145363692-dbac2a2b-68d1-4f7f-9ef0-34aca485b00f.png)
- Fukurou
![image](https://user-images.githubusercontent.com/71221969/145363809-225e2430-3c00-4b62-a4ba-f8283200a309.png)
- Blueno
![image](https://user-images.githubusercontent.com/71221969/145364078-8fbbc84c-ee1b-495e-a831-3bc628c665d5.png)
- Chiper
![image](https://user-images.githubusercontent.com/71221969/145364312-8a2e1811-d536-410b-b8ab-a3b401606a3c.png)

## Soal 2. Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan.
Langkah 1: panggil command berikut pada `Foosha`
```
iptables -A FORWARD -d 192.179.4.128/29 -i eth0 -p tcp --dport 80 -j DROP
```

## Soal 3. Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.
Langkah 1: panggil command berikut pada dns server dan dhcp server
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

Langkah 2: testing dengan melakukan ping pada dhcp server yaitu `Jipangu` dari 4 host, dimana ping ke 4 nantinya akan di tolak oleh servernya
- ping dari `Blueno`<br>
![image](https://user-images.githubusercontent.com/71221969/145365830-7e57a6db-ad46-444d-8dc7-b0802712ae1a.png)
- ping dari `Elena`<br>
![image](https://user-images.githubusercontent.com/71221969/145365952-a877b1ee-7190-4947-95cb-d3908a208345.png)
- ping dari `Chipper`<br>
![image](https://user-images.githubusercontent.com/71221969/145366057-d5b3049a-8e1d-4ea6-b913-6f000014691e.png)
- ping dari `Fukurou` akan di tolak karena pada dhcp server memang dibatasi hanya 3<br>
![image](https://user-images.githubusercontent.com/71221969/145366161-440168df-bde6-4109-b991-938a2dcf8967.png)

Langkah 3: testing dengan melakukan ping pada dns server yaitu `Doriki` dari 4 host, dimana ping ke 4 nantinya akan di tolak oleh servernya
- ping dari `Blueno`<br>
![image](https://user-images.githubusercontent.com/71221969/145368235-9e4940b2-b6cd-427f-a4f5-f5ac256788f0.png)
- ping dari `Elena`<br>
![image](https://user-images.githubusercontent.com/71221969/145368309-77e98596-b34f-4a4b-addc-d1709fbb0202.png)
- ping dari `Chipper`<br>
![image](https://user-images.githubusercontent.com/71221969/145368386-6c94a053-eab7-4788-9db8-dbf26571322f.png)
- ping dari `Fukurou` akan di tolak karena pada dhcp server memang dibatasi hanya 3<br>
![image](https://user-images.githubusercontent.com/71221969/145368476-3787ac72-ddb7-4b33-b6c9-938dc848a176.png)

## Soal 4. Akses dari subnet Blueno dan Cipher hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis.

## Soal 5. Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya.
