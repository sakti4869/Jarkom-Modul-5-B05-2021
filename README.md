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
