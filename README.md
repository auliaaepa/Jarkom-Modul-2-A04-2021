# Jarkom-Modul-2-A04-2021

**Nama Anggota Kelompok:**
* Julietta Anastasia Rodiah Boru Panjaitan (05111940000033)
* Aulia Eka Putri Aryani (05111940000044)
* Abdun Nafi’ (05111940000066)

Prefix IP: 10.1

## Soal 1
Pada soal ini diminta untuk membuat topologi beserta konfigurasinya.

**Penjelasan**
1. Buat topologi hingga diperoleh hasil sebagai berikut
![image](https://user-images.githubusercontent.com/76677130/139201546-d5b6fa13-01e8-4835-b151-103d649b8776.png)
2. Ubah masing - masing konfigurasi network melalui `Configure` > `Edit network configuration` 
   * Foosha
      ```
      auto eth0
      iface eth0 inet dhcp

      auto eth1
      iface eth1 inet static
        address 10.1.1.1
        netmask 255.255.255.0

      auto eth2
      iface eth2 inet static
        address 10.1.2.1
        netmask 255.255.255.0
      ```
   * Longuetown
      ```
      auto eth0
      iface eth0 inet static
        address 10.1.1.2
        netmask 255.255.255.0
        gateway 10.1.1.1
      ```
   * Alabasta
      ```
      auto eth0
      iface eth0 inet static
        address 10.1.1.3
        netmask 255.255.255.0
        gateway 10.1.1.1
      ```
   * EniesLobby
      ```
      auto eth0
      iface eth0 inet static
        address 10.1.2.2
        netmask 255.255.255.0
        gateway 10.1.2.1
      ```
   * Water7
      ```
      auto eth0
      iface eth0 inet static
        address 10.1.2.3
        netmask 255.255.255.0
        gateway 10.1.2.1
      ```
   * Skypie
      ```
      auto eth0
      iface eth0 inet static
        address 10.1.2.4
        netmask 255.255.255.0
        gateway 10.1.2.1
      ```
4. Hubungkan topologi dengan jaringan luar
   * Buka **Foosha** dan ketik command sebagai berikut
     ```
     iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.1.0.0/16
     ```
   * Buka semua node yang tersisa dan ketik command sebagai berikut
     ```
     echo nameserver 192.168.122.1 > /etc/resolv.conf
     ```


## Soal 2
Pada soal ini diminta untuk membuat website utama dengan domain **franky.a04.com** dan alias **www.franky.a04.com** pada `EniesLobby` di folder `kaizoku`.

**Penjelasan**
1. Buka **EniesLobby** 
2. Unduh bind9
    ```
    apt-get update
    apt-get install bind9 -y
    ```
3. Ubah **/etc/bind/named.conf.local** menjadi sebagai berikut
    ```
    zone "franky.a04.com" {
        type master;
        file "/etc/bind/kaizoku/franky.a04.com";
    };
    ```
4. Buat folder **kaizoku** di dalam **/etc/bind**
    ```
    mkdir /etc/bind/kaizoku
    ```
5. Copy **/etc/bind/db.local** ke **/etc/bind/kaizoku/franky.a04.com**
    ```
    cp /etc/bind/db.local /etc/bind/kaizoku/franky.a04.com
    ```
6. Ubah **/etc/bind/kaizoku/franky.a04.com** menjadi sebagai berikut
    ```
    $TTL    604800
    @       IN      SOA     franky.a04.com. root.franky.a04.com. (
                                  2         ; Serial
                             604800         ; Refresh
                              86400         ; Retry
                            2419200         ; Expire
                             604800 )       ; Negative Cache TTL
    ;
    @       IN      NS      franky.a04.com.
    @       IN      A       10.1.2.2        ; IP EniesLobby
    www     IN      CNAME   franky.a04.com.
    ```
7. Restart service bind9
    ```
    service bind9 restart
    ```

**Testing**
* Buka **Longuetown**
* Ketik command `ping -c 4 franky.a04.com`
* Ketik command `ping -c 4 www.franky.a04.com` 
![image](https://user-images.githubusercontent.com/76677130/139209546-45778282-d881-4271-b39c-a1a14474c2a6.png)



## Soal 3
Pada soal ini diminta untuk membuat subdomain **super.franky.a04.com** dengan alias **www.super.franky.a04.com** pada `EniesLobby` dan mengarah ke `Skypie`.

**Penjelasan**
1. Buka **EniesLobby**
2. Ubah **/etc/bind/kaizoku/franky.a04.com** menjadi sebagai berikut
    ```
    $TTL    604800
    @       IN      SOA     franky.a04.com. root.franky.a04.com. (
                                  3         ; Serial
                             604800         ; Refresh
                              86400         ; Retry
                            2419200         ; Expire
                             604800 )       ; Negative Cache TTL
    ;
    @       IN      NS      franky.a04.com.
    @       IN      A       10.1.2.2        ; IP EniesLobby
    www     IN      CNAME   franky.a04.com.
    super   IN      A       10.1.2.4        ; IP Skypie
    www.super.franky.a04.com.     IN      CNAME   super.franky.a04.com.
    ```
3. Restart service bind9
     ```
    service bind9 restart
    ```

**Testing**
* Buka **Longuetown**
* Ketik command `ping -c 4 super.franky.a04.com`
* Ketik command `ping -c 4 www.super.franky.a04.com` 
![image](https://user-images.githubusercontent.com/76677130/139212019-eb8a6b0f-5e1b-4c9a-870b-e5fc873f07a7.png)



## Soal 4
Pada soal ini diminta untuk membuat reverse domain dari domain utama (**franky.a04.com**).

**Penjelasan**
1. Buka **EnniesLobby**
2. Ubah **/etc/bind/named.conf.local** menjadi sebagai berikut
    ```
    zone "franky.a04.com" {
        type master;
        file "/etc/bind/kaizoku/franky.a04.com";
    };
    zone "2.1.10.in-addr.arpa" {
        type master;
        file "/etc/bind/kaizoku/2.1.10.in-addr.arpa";
    };
    ```
3. Copy **/etc/bind/db.local** ke **/etc/bind/kaizoku/2.1.10.in-addr.arpa**
    ```
    cp /etc/bind/db.local /etc/bind/kaizoku/2.1.10.in-addr.arpa
    ```
4. Ubah **/etc/bind/kaizoku/2.1.10.in-addr.arpa** menjadi sebagai berikut
    ```
    $TTL    604800
    @       IN      SOA     franky.a04.com. root.franky.a04.com. (
                                  2         ; Serial
                             604800         ; Refresh
                              86400         ; Retry
                            2419200         ; Expire
                             604800 )       ; Negative Cache TTL
    ;
    2.1.10.in-addr.arpa.    IN      NS      franky.a04.com.
    2                       IN      PTR     franky.a04.com. ; Byte ke-4 IP EniesLobby
    ```
5. Restart service bind9
     ```
    service bind9 restart
    ```

**Testing**
* Buka **Longuetown**
* Unduh dnsutils
    ```
    apt-get update
    apt-get install dnsutils -y
    ```
* Ketik command `host -t PTR 10.1.2.2`
![image](https://user-images.githubusercontent.com/76677130/139214648-1be858f8-99ae-4033-9490-4b1de3eec452.png)



## Soal 5
Pada soal ini diminta untuk membuat `Water7` sebagai DNS Slave untuk domain utama (**franky.a04.com**).

**Penjelasan**
1. Buka **EniesLobby**
2. Ubah **/etc/bind/named.conf.local** menjadi sebagai berikut
    ```
    zone "franky.a04.com" {
        type master;
        notify yes;
        also-notify { 10.1.2.3; };          // IP Water7
        allow-transfer { 10.1.2.3; };       // IP Water7
        file "/etc/bind/kaizoku/franky.a04.com";
    };
    zone "2.1.10.in-addr.arpa" {
        type master;
        file "/etc/bind/kaizoku/2.1.10.in-addr.arpa";
    };
    ```
3. Restart service bind9
    ```
    service bind9 restart
    ```
4. Buka **Water7**
5. Unduh bind9
    ```
    apt-get update
    apt-get install bind9 -y
    ```
6. Ubah **/etc/bind/named.conf.local** menjadi sebagai berikut
    ```
    zone "franky.a04.com" {
        type slave;
        masters { 10.1.2.2; };          // IP EniesLobby
        file "/var/lib/bind/franky.a04.com";
    };
    ```
7. Restart service bind9
     ```
    service bind9 restart
    ```

**Testing**
* Buka **EniesLobby**
* Matikan service bind9
    ```
    service bind9 stop
    ```
* Buka **Longuetown**
* Ketik command `ping -c 4 franky.a04.com`
![image](https://user-images.githubusercontent.com/76677130/139217144-8937b19d-063c-449a-bf87-75a0aeb09593.png)



## Soal 6
Pada soal ini diminta untuk mendelegasikan subdomain **mecha.franky.a04.com**, yang memiliki alias **www.mecha.franky.a04.com**, dari  `EniesLobby` ke `Water7` dengan IP menuju ke `Skypie` dalam folder `sunnygo`.

**Penjelasan**
1. Buka **EniesLobby**
2. Ubah **/etc/bind/kaizoku/franky.a04.com** menjadi sebagai berikut
    ```
    $TTL    604800
    @       IN      SOA     franky.a04.com. root.franky.a04.com. (
                                  4         ; Serial
                             604800         ; Refresh
                              86400         ; Retry
                            2419200         ; Expire
                             604800 )       ; Negative Cache TTL
    ;
    @       IN      NS      franky.a04.com.
    @       IN      A       10.1.2.2        ; IP EniesLobby
    www     IN      CNAME   franky.a04.com.
    super   IN      A       10.1.2.4        ; IP Skypie
    www.super.franky.a04.com.     IN      CNAME   super.franky.a04.com.
    ns1     IN      A       10.1.2.4        ; IP Skypie
    mecha   IN      NS      ns1
    ```
4. Ubah **/etc/bind/named.conf.options** menjadi sebagai berikut
    ```
    options {
        directory "/var/cache/bind";
        allow-query { any; };
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
5. Restart service bind9
    ```
    service bind9 restart
    ```
6. Buka **Water7**
7. Ubah **/etc/bind/named.conf.local** menjadi sebagai berikut
    ```
    zone "franky.a04.com" {
        type slave;
        masters { 10.1.2.2; };          // IP EniesLobby
        file "/var/lib/bind/franky.a04.com";
    };
    zone "mecha.franky.a04.com" {
        type master;
        file "/etc/bind/sunnygo/mecha.franky.a04.com";
    };
    ```
8. Buat direktori **sunnygo** di dalam **/etc/bind**
    ```
    mkdir /etc/bind/sunnygo
    ```
9. Copy **/etc/bind/db.local** ke **/etc/bind/sunnygo/mecha.franky.a04.com**
    ```
    /etc/bind/db.local /etc/bind/sunnygo/mecha.franky.a04.com
    ```
10. Ubah **/etc/bind/sunnygo/mecha.franky.a04.com** menjadi sebagai berikut
    ```   
    $TTL    604800
    @       IN      SOA     mecha.franky.a04.com. root.mecha.franky.a04.com. (
                                  2         ; Serial
                             604800         ; Refresh
                              86400         ; Retry
                            2419200         ; Expire
                             604800 )       ; Negative Cache TTL
    ;
    @       IN      NS      mecha.franky.a04.com.
    @       IN      A       10.1.2.4        ; IP Skypie
    www     IN      CNAME   mecha.franky.a04.com.
    ```
11. Ubah **/etc/bind/named.conf.options** menjadi sebagai berikut
    ```
    options {
        directory "/var/cache/bind";
        allow-query { any; };
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
12. Restart service bind9
     ```
    service bind9 restart
    ```

**Testing**
* Buka **Longuetown**
* Ketik command `ping -c 4 mecha.franky.a04.com`
* Ketik command `ping -c 4 www.mecha.franky.a04.com` 
![image](https://user-images.githubusercontent.com/76677130/139220659-290be6f0-0329-4f64-b4e8-08016f5bf0b6.png)



## Soal 7
Pada soal ini diminta untuk membuat subdomain **general.mecha.franky.a04.com** dengan alias **www.general.mecha.franky.a04.com** pada `Water7` dan mengarah ke `Skypie`.

**Penjelasan**
1. Buka **Water7**
2. Ubah **/etc/bind/sunnygo/mecha.franky.a04.com** menjadi sebagai berikut
    ```
    $TTL    604800
    @       IN      SOA     mecha.franky.a04.com. root.mecha.franky.a04.com. (
                                  3         ; Serial
                             604800         ; Refresh
                              86400         ; Retry
                            2419200         ; Expire
                             604800 )       ; Negative Cache TTL
    ;
    @       IN      NS      mecha.franky.a04.com.
    @       IN      A       10.1.2.4        ; IP Skypie
    www     IN      CNAME   mecha.franky.a04.com.
    general IN      A       10.1.2.4        ; IP Skypie
    www.general.mecha.franky.a04.com.       IN      CNAME   general.mecha.franky.a04.com.
    ```
3. Restart service bind9
     ```
    service bind9 restart
    ```

**Testing**
* Buka **Longuetown**
* Ketik command `ping -c 4 general.mecha.franky.a04.com`
* Ketik command `ping -c 4 www.general.mecha.franky.a04.com` 
![image](https://user-images.githubusercontent.com/76677130/139222372-8abcfcb3-e09d-481e-8229-f980503d9a92.png)



## Soal 8
Pada soal ini diminta untuk melakukan konfigurasi pada webserver **www.franky.a04.com** dengan DocumentRoot pada `/var/www/franky.a04.com`.

**Penjelasan**
1. Install apache2 beserta librarynya dan php
```
apt-get update
apt-get install apache2 -y
apt-get install libapache2-mod-php7.0 -y
apt-get install php -y
```
2. Copy /etc/apache2/sites-available/000-default.conf
```
cp /etc/apache2/sites-available/000-default.conf 
/etc/apache2/sites-available/franky.a04.com.conf
```
3. Ubah /etc/apache2/sites-available/franky.a04.com.conf
```
echo ‘<VirtualHost *:80>
        ServerName franky.a04.com
        ServerAlias www.franky.a04.com
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/franky.a04.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
‘ > /etc/apache2/sites-available/franky.a04.com.conf
```
4. Buat folder franky.a04.com di dalam /var/www
```
mkdir /var/www/franky.a04.com
```
5. Download file website
```
wget 
https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/franky.zip
```
6. Unzip file dari website
```
unzip franky.zip 
```
7. Pindahkan isi semua file website ke folder yang telah dibuat
```
mv franky/* /var/www/franky.a04.com
```
8. Aktifkan konfigurasi website
```
a2ensite franky.a04.com.conf
```
9. Restart apache
```
service apache2 restart
```
10. Pastikan semua Record A dan PTR pada **/etc/bind/kaizoku/franky.a04.com**, **/etc/bind/kaizoku/2.1.10.in-addr.arpa**, dan **/etc/bind/sunnygo/mecha.franky.a04.com** mengarah ke IP Skypie (10.1.2.4)
![image](https://user-images.githubusercontent.com/76677130/139524799-12a45015-4fbd-4140-8ff0-567a34af419e.png)
11. Restart service bind9
    ```
    service bind9 restart
    ```
12. Buka **Skypie**
6. nano
![image](https://user-images.githubusercontent.com/76677130/139525906-b27e018b-c5ba-4181-9c15-068620ff9b99.png)
ls
![image](https://user-images.githubusercontent.com/76677130/139525409-5cfb9e39-8ade-4c4f-b18b-0ad442fe0d1c.png)

**Testing**
* Buka **Longuetown**
* Unduh lynx
    ```
    apt-get update
    apt-get install lynx -y
    ```
* Ketik command `lynx www.franky.a04.com`
![8t](https://user-images.githubusercontent.com/76677130/139525954-3578867e-b1a4-44db-870a-299c429e2055.PNG)



## Soal 9
Pada soal ini diminta untuk mengubah url **www.franky.a04.com/index.php/home** menjadi **www.franky.a04.com/home**.

**Penjelasan**
1. Buka **Skypie**
2. a
3. s
.ht
![image](https://user-images.githubusercontent.com/76677130/139526346-4b38feb1-33c6-49b7-bc50-19b46ebccede.png)
ubah
![9b](https://user-images.githubusercontent.com/76677130/139526512-2b156b78-9d93-43f3-bc49-c715d7370f05.PNG)

**Testing**
* Buka **Longuetown**
* Ketik command `lynx www.franky.a04.com/home`
![image](https://user-images.githubusercontent.com/76677130/139526509-46ed34ec-10fb-415b-80e5-99adf78e6635.png)



## Soal 10
Pada soal ini diminta untuk melakukan konfigurasi pada webserver **www.super.franky.a04.com** dengan DocumentRoot pada `/var/www/super.franky.a04.com`.

**Penjelasan**
1. Buka **Skypie**
2. a
3. s
ubah
![image](https://user-images.githubusercontent.com/76677130/139526809-7d85cc6f-95b5-4d53-8368-ddc110682d33.png)
ls
![image](https://user-images.githubusercontent.com/76677130/139526981-e7639f95-014c-4fbd-b0ac-12ec7a8e6bf7.png)

**Testing**
* Buka **Longuetown**
* Ketik command `lynx www.super.franky.a04.com`
![image](https://user-images.githubusercontent.com/76677130/139527130-13071e88-f319-4bec-80e5-ac407dbbaa20.png)



## Soal 11
Pada soal ini diminta untuk hanya membuat directory listing pada folder `public` di webserver **www.super.franky.a04.com**.

**Penjelasan**
1. Buka **Skypie**
2. a
3. s
ubah
![image](https://user-images.githubusercontent.com/76677130/139527385-9393ac8d-536e-4324-912a-1283e2b0f712.png)

**Testing**
* Buka **Longuetown**
* Ketik command `lynx www.super.franky.a04.com/public`
![11b](https://user-images.githubusercontent.com/76677130/139527545-dab3dca5-2058-41d0-a8ad-3911ff9d70ab.PNG)
* Klik pada salah satu directory (`js/`)
![11c](https://user-images.githubusercontent.com/76677130/139527546-f1250864-d076-4bb8-9452-269131165e1e.PNG)



## Soal 12
Pada soal ini diminta untuk mengganti error kode pada webserver **www.super.franky.a04.com** dengan file `404.html` yang berada dalam folder `error`.

**Penjelasan**
1. Buka **Skypie**
2. a
3. s
ubah
![image](https://user-images.githubusercontent.com/76677130/139527647-12754e18-b840-42ee-aa6f-929b367bb3cd.png)



## Soal 13
Pada soal ini diminta untuk membuat konfigurasi vitual host yang dapat memberikan akses **www.super.franky.a04.com/js** kepada **www.super.franky.a04.com/public/js**.

**Penjelasan**
1. Buka **Skypie**
2. a
3. s
ubah
![image](https://user-images.githubusercontent.com/76677130/139527965-0ac9684d-91d6-41c1-951e-951c36db3096.png)

**Testing**
* Buka **Longuetown**
* Ketik command `lynx www.super.franky.a04.com/js` (dilakukan sebelum nomor 11)
![image](https://user-images.githubusercontent.com/76677130/139528291-881d8c8a-e181-4212-8e17-ad4d8b5a0e55.png)



## Soal 14
Pada soal ini diminta untuk melakukan konfigurasi pada webserver **www.general.mecha.franky.a04.com** dengan port `15000` dan `15500` serta DocumentRoot terletak pada `/var/www/general.mecha.franky.a04.com`.

**Penjelasan**
1. Buka **Skypie**
2. a
3. s
ubah
![image](https://user-images.githubusercontent.com/76677130/139528985-a2e93f97-be16-48cc-be42-190449ec2920.png)
port
![image](https://user-images.githubusercontent.com/76677130/139528684-cebf093f-0d3c-44b0-970a-7a8b4e26d0ba.png)
ls
![image](https://user-images.githubusercontent.com/76677130/139529080-ed714e4c-047f-4e83-bb75-dcb9fff79f6b.png)

**Testing**
* Buka **Longuetown**
* Ketik command `lynx 10.1.2.4:15000`
![image](https://user-images.githubusercontent.com/76677130/139529052-1a58ec6e-56ac-4e91-a8ff-542b60b262d9.png)



## Soal 15
Pada soal ini diminta untuk memberikan autentifikasi dengan username `luffy` dan password `onepiece` pada webserver **www.general.mecha.franky.a04.com**.

**Penjelasan**
1. Buka **Skypie**
2. a
3. s
htpass
![image](https://user-images.githubusercontent.com/76677130/139529233-81523d73-24ea-42f0-afd8-0ca597f33590.png)
uabh
![image](https://user-images.githubusercontent.com/76677130/139529281-3b298efc-d55e-4259-ac99-ef1d99064592.png)
.htaccess
![image](https://user-images.githubusercontent.com/76677130/139529475-915510b3-4db1-4c9d-85f7-e066eee7a393.png)

**Testing**
* Buka **Longuetown**
* Ketik command `lynx 10.1.2.4:15500`
* Masukkan username `luffy`
![15t1](https://user-images.githubusercontent.com/76677130/139529413-f0f256a3-5b1c-42c4-bf7a-699da4d3913f.PNG)
* Masukkan password `one piece`
![15t2](https://user-images.githubusercontent.com/76677130/139529418-6621192a-2a15-44d8-816b-272662d7ea86.PNG)
* Tampil halaman web
![15t3](https://user-images.githubusercontent.com/76677130/139529419-6d985ab2-7638-4983-8beb-990381da1e79.PNG)



## Soal 16
Pada soal ini diminta untuk mengarahkan IP Skypie (**10.1.2.4**) ke **www.franky.a04.com**.

**Penjelasan**
1. Buka **Skypie**
2. a
3. s
.htaccess
![image](https://user-images.githubusercontent.com/76677130/139529941-591385a1-5956-4c8f-b3fa-fe07fe86e5de.png)
ubah
![image](https://user-images.githubusercontent.com/76677130/139529656-116540d4-6655-4d16-b22a-219c557e9b9d.png)

**Testing**
* Buka **Longuetown**
* Ketik command `lynx 10.1.2.4`
![16t](https://user-images.githubusercontent.com/76677130/139529951-4d39d3c8-b191-4887-9103-fe180dd547c1.PNG)



## Soal 17
Pada soal ini diminta untuk mengarahkan request gambar yang memiliki substring `franky`ke `franky.png` pada webserver **www.super.franky.a04.com**.

**Penjelasan**
1. Buka **Skypie**
2. a
3. s
.htaccess
![17a](https://user-images.githubusercontent.com/76677130/139530625-52df9680-112e-4a63-bb64-eb7270d04405.PNG)
ubah
![17b](https://user-images.githubusercontent.com/76677130/139530636-6e51be58-7fa6-47ce-bc21-5a2f1969ebc5.PNG)

**Testing**
* Buka **Longuetown**
* Ketik command `lynx www.super.franky.a04.com/afranky12.png` (dilakukan sebelum nomor 11)
![image](https://user-images.githubusercontent.com/76677130/139530612-82ad6716-e7f6-4edd-b12a-b49ed61a1381.png)



## Kendala
Adapun kendala yang dialami selama pengerjaan soal praktikum modul ini adalah sebagai berikut.
* lynx tidak dapat menampilkan gambar
* terkadang gns3 tidak terhubung dengan jaringan internet (tidak dapat download dll)
