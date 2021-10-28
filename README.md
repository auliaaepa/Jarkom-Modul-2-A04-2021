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
**Hasil Testing**
![image](https://user-images.githubusercontent.com/76677130/139209546-45778282-d881-4271-b39c-a1a14474c2a6.png)



## Soal 3
Pada soal ini diminta untuk membuat subdomain **super.franky.yyy.com** dengan alias **www.super.franky.yyy.com** pada `EniesLobby` dan mengarah ke `Skypie`.

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
**Hasil Testing**
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
**Hasil Testing**
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
**Hasil Testing**
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
**Hasil Testing**
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
**Hasil Testing**
![image](https://user-images.githubusercontent.com/76677130/139222372-8abcfcb3-e09d-481e-8229-f980503d9a92.png)


