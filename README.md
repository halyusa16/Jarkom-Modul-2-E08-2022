# Jarkom-Modul-2-E08-2022
Laporan Resmi Praktikum II Jaringan Komputer oleh Kelompok E08

### Kelompok E08, PREFIX IP: 192.196

| **No** | **Nama** | **NRP** |
| - | - | - |
| 1. | Halyusa Ard Wahyudi | 5025201088 |
| 2. | Muhammad Ismail | 5025201223 |
| 3. | Immanuel Maruli Tua Pardede | 5025201166 |

Twilight (〈黄昏 (たそがれ) 〉, <Tasogare>) adalah seorang mata-mata yang berasal dari negara Westalis. Demi menjaga perdamaian antara Westalis dengan Ostania, Twilight dengan nama samaran Loid Forger (ロイド・フォージャー, Roido Fōjā) di bawah organisasi WISE menjalankan operasinya di negara Ostania dengan cara melakukan spionase, sabotase, penyadapan dan kemungkinan pembunuhan. Berikut adalah peta dari negara Ostania:

<img src="https://github.com/immanuelmtpardede/Jarkom-Modul-2-E08-2022/blob/main/img/intro.png" width=50%>

## No. 1
### Soal
WISE akan dijadikan sebagai DNS Master, Berlint akan dijadikan DNS Slave, dan Eden akan digunakan sebagai Web Server. Terdapat 2 Client yaitu SSS, dan Garden. Semua node terhubung pada router Ostania, sehingga dapat mengakses internet (1).
### Jawaban
Pertama, kita buat topologi sesuai dengan tuntutan soal. Kemudian, kita atur konfigurasi masing-masing node berikut dengan cara klik kanan pada node tersebut, lalu pilih `Configure` dan `Edit network configuration`.

<img src="https://github.com/immanuelmtpardede/Jarkom-Modul-2-E08-2022/blob/main/img/1.0.png" width=50%>

<ul>

<li>Ostania</li>

```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.196.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.196.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.196.3.1
	netmask 255.255.255.0
```

<li>SSS</li>

```
auto eth0
iface eth0 inet static
	address 192.196.1.2
	netmask 255.255.255.0
	gateway 192.196.1.1
```

<li>Garden</li>

```
auto eth0
iface eth0 inet static
	address 192.196.1.3
	netmask 255.255.255.0
	gateway 192.196.1.1
```

<li>Berlint</li>

```
auto eth0
iface eth0 inet static
	address 192.196.2.2
	netmask 255.255.255.0
	gateway 192.196.2.1
```

<li>Eden</li>

```
auto eth0
iface eth0 inet static
	address 192.196.2.3
	netmask 255.255.255.0
	gateway 192.196.2.1
```

<li>WISE</li>

```
auto eth0
iface eth0 inet static
	address 192.196.3.2
	netmask 255.255.255.0
	gateway 192.196.3.1
```

</ul>

Kemudian, agar dapat mengakses jaringan luar, kita klik kanan pada node Ostania, lalu pilih `Web console` untuk membuka web console dan ketikkan `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.196.0.0/16`. Selain itu, kita juga perlu mengubah nameserver pada masing-masing node (node SSS, Garden, Berlint, Eden, dan WISE) menggunakan IP DNS yang sama pada node Ostania. Untuk melihatnya, ketikkan command `nano /etc/resolv.conf` pada web console node Ostania dan copy-kan isinya. Kemudian, ketikkan command `nano /etc/resolv.conf` pada web console masing-masing node dan ganti isinya dengan yang sudah di-copy tadi. Terakhir, untuk mengecek apakah klien sudah terhubung dengan jaringan luar, ketikkan command `ping google.com` pada web console masing-masing node.

<img src="https://github.com/immanuelmtpardede/Jarkom-Modul-2-E08-2022/blob/main/img/1.1.png" width=50%>

## No. 2
### Soal
Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.yyy.com dengan alias www.wise.yyy.com pada folder wise.
### Jawaban
Langkah awal yang perlu kita lakukan adalah mengupdate sistem node WISE agar dapat menginstal aplikasi bind9. Ketikkan command `apt-get update` pada web console node WISE untuk mengupdate sistemnya, lalu `apt-get install bind9 -y` untuk menginstal aplikasi bind9. Setelah itu, buka file `named.conf.local` dengan cara mengetik command `nano /etc/bind/named.conf.local` pada web console node WISE. Kemudian ganti isinya dengan script berikut.

```
zone "wise.e08.com" {
	type master;
	file "/etc/bind/wise/wise.e08.com";
};
```

Kita perlu membuat file bernama `wise.e08.com` dengan di folder `/etc/bind/wise/`, untuk itu ketik command `mkdir /etc/bind/wise` untuk membuat folder wise dan `cp /etc/bind/db.local /etc/bind/wise/wise.e08.com` untuk membuat salinan file `db.local` sebagai file `wise.e08.com`. Kemudian, buka filenya dengan mengetik command `nano /etc/bind/wise/wise.e08.com` dan ganti isinya sesuai dengan script berikut.

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.e08.com. root.wise.e08.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      wise.e08.com.
@       IN      A       192.196.3.2 ; IP WISE
@       IN      AAAA    ::1
www     IN      CNAME   wise.e08.com.
```

Terakhir, ketik command `service bind9 restart` pada web console node WISE untuk me-restart aplikasi bind9 beserta seluruh konfigurasinya.
Untuk mengecek apakah website utama kita sudah selesai dan bisa diakses oleh klien (node SSS dan Garden), pertama-tama kita harus mengubah konfigurasi nameserver pada klien. Ketik `nano /etc/resolv.conf` pada web console klien dan ganti isinya dengan IP DNS Master (node WISE) yaitu 192.196.3.2 `nameserver 192.196.3.2 # IP WISE`. Kemudian, `ping wise.e08.com` dan `ping www.wise.e08.com` untuk mengecek kesuksesannya.

<img src="https://github.com/immanuelmtpardede/Jarkom-Modul-2-E08-2022/blob/main/img/2.0.png" width=50%>

## No. 3
### Soal
Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden.
### Jawaban
Pertama, kita buka file `wise.e08.com` dengan command `nano /etc/bind/wise/wise.e08.com`. Kemudian, tambahkan script `eden    IN      A       192.196.2.3 ; IP Eden` di bagian paling bawah script untuk membuat subdomain eden.wise.yyy.com. Lalu, untuk membuat aliasnya, kita perlu membuat zone baru. Ketik command `nano /etc/bind/named.conf.local` untuk membuka file `named.conf.local`, lalu tambahkan script berikut di bagian paling bawah script untuk membuat zone baru subdomain eden.wise.yyy.com.

```
zone "eden.wise.e08.com" {
        type master;
        file "/etc/bind/wise/eden.wise.e08.com";
};
```

Sama seperti langkah di nomor 2, kita perlu membuat file baru bernama `eden.wise.e08.com` di folder `/etc/bind/wise/`. Ketik command `cp /etc/bind/db.local /etc/bind/wise/eden.wise.e08.com` untuk membuat salinan file `db.local` sebagai file `eden.wise.e08.com`. Kemudian, buka filenya dengan mengetik command `nano /etc/bind/wise/eden.wise.e08.com` dan ganti isinya sesuai dengan script berikut.

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     eden.wise.e08.com. root.eden.wise.e08.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      eden.wise.e08.com.
@       IN      A       192.196.2.3 ; IP Eden
www     IN      CNAME   eden.wise.e08.com.
```

Terakhir, ketik command `service bind9 restart` pada web console node WISE untuk me-restart aplikasi bind9 beserta seluruh konfigurasinya. Kemudian, `ping eden.wise.e08.com` dan `ping www.eden.wise.e08.com` pada web console klien untuk mengecek kesuksesannya.

<img src="https://github.com/immanuelmtpardede/Jarkom-Modul-2-E08-2022/blob/main/img/3.0.png" width=50%>

## No. 4
### Soal
Buat juga reverse domain untuk domain utama.
### Jawaban
Kita perlu membuat zone baru. Ketik command `nano /etc/bind/named.conf.local` pada web console node WISE untuk membuka file `named.conf.local`, lalu tambahkan script berikut di bagian paling bawah script.

```
zone "3.196.192.in-addr.arpa" {
        type master;
        file "/etc/bind/wise/3.196.192.in-addr.arpa";
};
```

Sama seperti langkah di nomor 2 dan 3, kita perlu membuat file baru bernama `3.196.192.in-addr.arpa` di folder `/etc/bind/wise/`. Ketik command `cp /etc/bind/db.local /etc/bind/wise/3.196.192.in-addr.arpa` untuk membuat salinan file `db.local` sebagai file `3.196.192.in-addr.arpa`. Kemudian, buka filenya dengan mengetik command `nano /etc/bind/wise/3.196.192.in-addr.arpa` dan ganti isinya sesuai dengan script berikut.

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.e08.com. root.wise.e08.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
3.196.192.in-addr.arpa.       IN      NS      wise.e08.com.
2                             IN      PTR     wise.e08.com.
```

Terakhir, ketik command `service bind9 restart` pada web console node WISE untuk me-restart aplikasi bind9 beserta seluruh konfigurasinya. Kemudian, untuk mengecek kesuksesannya, ketik command `host -t PTR 192.196.3.2` pada web console klien. Pastikan sudah menginstal package dnsutils. Jika belum, ketik command `apt-get install dnsutils` pada web console klien sebelumnya. Hal yang perlu juga diperhatikan agar dapat menginstal package dnsutils yaitu ubah nameserver pada klien di /etc/resolv.conf sesuai dengan nameserver pada node Ostania di /etc/resolv.conf dan lakukan update dengan cara mengetik command `apt-get update` pada web console klien. Jangan lupa untuk mengubah kembali nameserver pada klien di /etc/resolv.conf sesuai dengan IP DNS Master `nameserver 192.196.3.2 # IP WISE` agar command `host -t PTR 192.196.3.2` berjalan sukses.

<img src="https://github.com/immanuelmtpardede/Jarkom-Modul-2-E08-2022/blob/main/img/4.0.png" width=50%>

## No. 5
### Soal
Agar dapat tetap dihubungi jika server WISE bermasalah, buatlah juga Berlint sebagai DNS Slave untuk domain utama.
### Jawaban
Kita mengedit file `named.conf.local` dengan cara mengetik command `nano /etc/bind/named.conf.local` pada web console node WISE dan menambahkan script berikut di zone wise.e08.com. Kita menggunakan IP Berlin karena node Berlint merupakan DNS Slave kita.

```
        notify yes;
        also-notify { 192.196.2.2; }; // IP Berlint
        allow-transfer { 192.196.2.2; }; // IP Berlint

```

sehingga menjadi

```
zone "wise.e08.com" {
        type master;
        notify yes;
        also-notify { 192.196.2.2; }; // IP Berlint
        allow-transfer { 192.196.2.2; }; // IP Berlint
        file "/etc/bind/wise/wise.e08.com";
};
```

Lalu, lakukan restart aplikasi bind9 dengan mengetik command `service bind9 restart` pada web console node WISE.
Kita belum selesai. Kita perlu membuat zone wise.e08.com pada node Berlint. Oleh karena itu, lakukan instalasi aplikasi bind9 pada node Berlint sama seperti langkah di nomor 2, `apt-get update` pada web console node Berlint untuk mengupdate sistemnya dan `apt-get install bind9 -y` untuk menginstal aplikasi bind9. Kemudian, buka file `named.conf.local` di folder `/etc/bind/` dengan mengetik command `nano /etc/bind/named.conf.local` pada web console node Berlint. Ganti isinya dengan script berikut.

```
zone "wise.e08.com" {
    type slave;
    masters { 192.196.3.2; }; // IP WISE
    file "/var/lib/bind/wise.e08.com";
};
```
Terakhir, lakukan restart aplikasi bind9 dengan mengetik command `service bind9 restart` pada web console node Berlint. Untuk mengecek apakah node Berlint sudah berjalan dengan sukses sebagai DNS Slave, kita matikan aplikasi bind9 pada node WISE sebagai DNS Master dengan mengetik `service bind9 stop` pada web console node WISE. Selain itu, kita juga perlu menambahkan IP DNS Slave pada konfigurasi nameserver klien. Ketik command `nano /etc/resolv.conf` pada web console klien dan tambahkan script `nameserver 192.196.2.2 # IP Berlint` di bagian paling bawah script. Karena `ping wise.e08.com`, `ping www.wise.e08.com`, dan `ping eden.wise.e08.com` masih dapat dilakukan, berarti DNS Slave berjalan dengan sukses.

<img src="https://github.com/immanuelmtpardede/Jarkom-Modul-2-E08-2022/blob/main/img/5.0.png" width=50%>

## No. 6
### Soal
Karena banyak informasi dari Handler, buatlah subdomain yang khusus untuk operation yaitu operation.wise.yyy.com dengan alias www.operation.wise.yyy.com yang didelegasikan dari WISE ke Berlint dengan IP menuju ke Eden dalam folder operation.
### Jawaban
Pertama, kita buka file `wise.e08.com` dengan command `nano /etc/bind/wise/wise.e08.com`. Kemudian, tambahkan script `operation IN    A       192.196.2.2 ; IP Berlint` di bagian paling bawah script untuk membuat subdomain operation.wise.yyy.com. Kemudian, edit konfigurasi file `named.conf.options` dengan mengetik `nano /etc/bind/named.conf.options`. Jadikan penggalan script **dnssec-validation auto;** sebagai komentar dengan menambahkan "//" di depannya yaitu `//dnssec-validation auto;` dan tambahakan penggalan script `allow-query{any;};` di bawahnya. Terakhir, lakukan restart aplikasi bind9 dengan mengetik command `service bind9 restart` pada web console node WISE.

<img src="https://github.com/immanuelmtpardede/Jarkom-Modul-2-E08-2022/blob/main/img/6.0.png" width=50%>

Pada node Berlint, kita menambahkan zone baru dengan mengedit file `named.conf.local` pada folder `nano /etc/bind/`. Ketik command `nano /etc/bind/named.conf.local` pada web console node Berlint dan tambahkan script berikut di bagian paling bawah script.

```
zone "operation.wise.e08.com" {
    type master;
    file "/etc/bind/operation/operation.wise.e08.com";
};
```

Kita perlu membuat file bernama `operation.wise.e08.com` dengan di folder `/etc/bind/operation/`, untuk itu ketik command `mkdir /etc/bind/operation/` untuk membuat folder operation dan `cp /etc/bind/db.local /etc/bind/operation/operation.wise.e08.com` untuk membuat salinan file `db.local` sebagai file `operation.wise.e08.com`. Kemudian, buka filenya dengan mengetik command `nano /etc/bind/operation/operation.wise.e08.com` dan ganti isinya sesuai dengan script berikut. Perhatikan bahwa IP yang digunakan adalah IP Eden sesuai yang diminta pada soal.

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     operation.wise.e08.com. root.operation.wise.e08.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      operation.wise.e08.com.
@       IN      A       192.196.2.3 ; IP Eden
www     IN      CNAME   operation.wise.e08.com.
```

Terakhir, lakukan restart aplikasi bind9 dengan mengetik command `service bind9 restart` pada web console node Berlint. Kemudian, `ping operation.wise.e08.com` dan `ping www.operation.wise.e08.com` pada web console klien untuk mengecek kesuksesannya.

<img src="https://github.com/immanuelmtpardede/Jarkom-Modul-2-E08-2022/blob/main/img/6.1.png" width=50%>

## No. 7
### Soal
Untuk informasi yang lebih spesifik mengenai Operation Strix, buatlah subdomain melalui Berlint dengan akses strix.operation.wise.yyy.com dengan alias www.strix.operation.wise.yyy.com yang mengarah ke Eden.
### Jawaban
Buka file `named.conf.local` dengan cara mengetik command `nano /etc/bind/named.conf.local` pada web console node Berlint. Kemudian tambahkan script berikut di bagian paling bawah script.

```
zone "strix.operation.wise.yyy.com" {
	type master;
	file "/etc/bind/operation/strix.operation.wise.yyy.com";
};
```

Kita perlu membuat file bernama `strix.operation.wise.yyy.com` di folder `/etc/bind/operation/`, untuk itu ketik command `cp /etc/bind/db.local /etc/bind/operation/strix.operation.wise.yyy.com` untuk membuat salinan file `db.local` sebagai file `strix.operation.wise.yyy.com`. Kemudian, buka filenya dengan mengetik command `nano /etc/bind/operation/strix.operation.wise.yyy.com` dan ganti isinya sesuai dengan script berikut.

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     strix.operation.wise.yyy.com. root.strix.operation.wise.yyy.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      strix.operation.wise.yyy.com.
@       IN      A       192.196.2.3 ; IP Eden
@       IN      AAAA    ::1
www     IN      CNAME   strix.operation.wise.yyy.com.
```

Terakhir, ketik command `service bind9 restart` pada web console node Berlint untuk me-restart aplikasi bind9 beserta seluruh konfigurasinya.
Untuk mengecek apakah website kita sudah selesai dan bisa diakses oleh klien (node SSS dan Garden), `ping strix.operation.wise.yyy.com` dan `ping www.strix.operation.wise.yyy.com`.

<img src="https://github.com/immanuelmtpardede/Jarkom-Modul-2-E08-2022/blob/main/img/7.0.png" width=50%>

## No. 8
### Soal

### Jawaban

## No. 9
### Soal

### Jawaban

## No. 10
### Soal

### Jawaban

## No. 11
### Soal

### Jawaban

## No. 12
### Soal

### Jawaban

## No. 13
### Soal

### Jawaban

## No. 14
### Soal

### Jawaban

## No. 15
### Soal

### Jawaban

## No. 16
### Soal

### Jawaban

## No. 17
### Soal

### Jawaban
