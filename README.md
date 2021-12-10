# Jarkom-Modul-5-B05-2021

## Anggota Kelompok ##

Aji Wahyu Admaja Utama 05111940000073

Muhammad Haikal Aria Sakti 05111940000088

Ivan Muhammad Nizar 05111940000052

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
        option domain-name-servers 192.179.4.131;
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
- Elena<br>
![image](https://user-images.githubusercontent.com/71221969/145363692-dbac2a2b-68d1-4f7f-9ef0-34aca485b00f.png)
- Fukurou<br>
![image](https://user-images.githubusercontent.com/71221969/145363809-225e2430-3c00-4b62-a4ba-f8283200a309.png)
- Blueno<br>
![image](https://user-images.githubusercontent.com/71221969/145364078-8fbbc84c-ee1b-495e-a831-3bc628c665d5.png)
- Chiper<br>
![image](https://user-images.githubusercontent.com/71221969/145364312-8a2e1811-d536-410b-b8ab-a3b401606a3c.png)

## Soal 2. Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan.
Langkah 1: panggil command berikut pada `Foosha`
```
iptables -A FORWARD -d 192.179.4.128/29 -i eth0 -p tcp --dport 80 -j DROP
```
Langkah 2: Testing 
- install netcat di server Jipangu dan Doriki: `apt-get install netcat`
- Pada Jipangu dan Doriki ketikkan: `nc -l -p 80`
- Pada foosha ketikkan: `nmap -p 80 192.179.4.131` atau `nmap -p 192.179.4.130`<br>
Foosha:<br>
![image](https://user-images.githubusercontent.com/71221969/145403877-0fc5f913-572b-4836-bf3e-9ce848c9695a.png)
![image](https://user-images.githubusercontent.com/71221969/145404013-687c219a-ac1d-4a24-8dff-7c14374722ab.png)
<br>Doriki:<br>
![image](https://user-images.githubusercontent.com/71221969/145404056-05957fd7-6e23-45b6-9654-8fd9e5d22fce.png)
<br>Jipangu:<br>
![image](https://user-images.githubusercontent.com/71221969/145404101-29670155-c754-4bb3-a051-583fd9b23bb7.png)

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
Langkah 1: pada `Doriki`
- untuk `Chipper`
```
iptables -A INPUT -s 192.179.0.0/22 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 192.179.0.0/22 -j REJECT
```
- untuk `Blueno`
```
iptables -A INPUT -s 192.179.4.0/25 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 192.179.4.0/25 -j REJECT
```
Langkah 2: Testing pada `Chipper`
- berhasil<br>
![image](https://user-images.githubusercontent.com/71221969/145382963-dd030fad-aa30-4e4e-9e81-9c6954e52dee.png)
- gagal<br>
![image](https://user-images.githubusercontent.com/71221969/145386599-cc6492b5-5e94-4d06-9c12-96c9769bcac1.png)
Langkah 3: Testing pada `Blueno`
- berhasil<br>
![image](https://user-images.githubusercontent.com/71221969/145497155-3e8f3a8d-9ab5-49b0-8945-5fab29275209.png)
- gagal<br>
![image](https://user-images.githubusercontent.com/71221969/145497201-99b042a7-e8d1-4702-a3e6-10209f08a28c.png)

## Soal 5. Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya.
Langkah 1: pada `Doriki`
- untuk `Elena`
```
iptables -A INPUT -s 192.179.16.0/23 -m time --timestart 07:00 --timestop 15:00 -j REJECT
```
- untuk `Fukurou`
```
iptables -A INPUT -s 192.179.18.0/24 -m time --timestart 07:00 --timestop 15:00 -j REJECT
```
Langkah 2: Testing pada `Elena`
- berhasil<br>
![image](https://user-images.githubusercontent.com/71221969/145495422-e27865c3-2f37-4062-b010-5cfc8202f827.png)
- gagal<br>
![image](https://user-images.githubusercontent.com/71221969/145495225-c4c5f5a1-3d26-479b-93fb-57a7d2f89dfe.png)
<br>
Langkah 3: Testing pada `Fukurou`<br>
- berhasil<br>
![image](https://user-images.githubusercontent.com/71221969/145495587-04969f1d-e8c5-4776-8894-eed7a2d08efb.png)
- gagal
![image](https://user-images.githubusercontent.com/71221969/145497035-3d13aa84-8723-47b3-8da0-8accf181e8c3.png)

## Soal 6. Karena kita memiliki 2 Web Server, Luffy ingin Guanhao disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Jorge dan Maingate
- pada `Doriki`:
    1. membuat domain (DNS) yang mengarah ke IP random (dalam hal ini `192.179.21.1`) pada file `/etc/bind/named.conf`
        ```
        zone "jarkomb05.com" {
            type master;
            file "/etc/bind/jarkom/jarkomb05.com";
        };
        ```
    2. Buat folder jarkom dengan command:
        ```
        mkdir /etc/bind/jarkom
        ```
    3. Lalu copy `db.local` ke file `/etc/bind/jarkom/jarkomb05.com`
        ```
        cp /etc/bind/db.local /etc/bind/jarkom/jarkomb05.com
        ```
    4. Lalu edit file `/etc/bind/jarkom/jarkomb05.com`
        ![image](https://user-images.githubusercontent.com/71221969/145499877-81c9611f-d136-4030-b722-157e9c5ccfba.png)
- pada `Guanhao`
```
iptables -A PREROUTING -t nat -p tcp -d 192.179.21.1 --dport 80 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.179.19.2:80
iptables -A PREROUTING -t nat -p tcp -d 192.179.21.1 --dport 80 -j DNAT --to-destination 192.179.19.3:80
iptables -t nat -A POSTROUTING -p tcp -d 192.179.19.2 --dport 80 -j SNAT --to-source 192.179.21.1:80
iptables -t nat -A POSTROUTING -p tcp -d 192.179.19.3 --dport 80 -j SNAT --to-source 192.179.21.1:80
```
- Testing
    - Pada `Guanhao`, `Jorge`, `Maingate`, `Elena` dan `Fukurou` install `apt-get install netcat`
    - Pada Jorge ketikkan perintah: `nc -l -p 80`
    - Pada Maingate ketikkan perintah: `nc -l -p 80`
    - Pada client Elena dan fukurou ketikkan perintah: `nc 192.179.21.1 80`
    - Ketikkan sembarang pada client Elena dan fukurou, nanti akan muncul bergantian
- Pada `Elena`<br>
![image](https://user-images.githubusercontent.com/71221969/145501511-7504937b-82c2-4376-8a80-e24b6afa3f9d.png)
- Pada `Fukurou`<br>
![image](https://user-images.githubusercontent.com/71221969/145501554-da1dcb3a-542a-4b34-a2a7-bffacee30b8d.png)
- Pada `Jorge`<br>
![image](https://user-images.githubusercontent.com/71221969/145501591-c2f8cfc7-9ee6-4fa7-82c0-30d356e4d2b5.png)
- Pada `Maingate`<br>
![image](https://user-images.githubusercontent.com/71221969/145501633-6657c2b8-49d8-47aa-89ff-8cfa76d0fb20.png)

## Kendala:
- Sempat mengalami kendala dalam testing pada nomor 2 dan nomor 6
