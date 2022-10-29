# Jarkom-Modul-2-E08-2022
Laporan Resmi Praktikum II Jaringan Komputer oleh Kelompok E08

### Kelompok E08, PREFIX IP: 192.196

| **No** | **Nama** | **NRP** | **Kontribusi (No.)** |
| - | - | - | - |
| 1. | Halyusa Ard Wahyudi | 5025201088 | 1, 2, dan 7 |
| 2. | Muhammad Ismail | 5025201223 | 3, 4, 5, dan 6 |
| 3. | Immanuel Maruli Tua Pardede | 5025201166 | 8, 9, dan 10 |

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

Kemudian, agar dapat mengakses jaringan luar, kita klik kanan pada node Ostania, lalu pilih `Web console` untuk membuka web console dan ketikkan `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.196.0.0/16`. Selain itu, kita juga perlu mengubah nameserver pada klien (node SSS dan Garden) menggunakan IP DNS yang sama pada node Ostania. Untuk melihatnya, ketikkan command `nano /etc/resolv.conf` pada web console node Ostania dan copy-kan isinya. Kemudian, ketikkan command `nano /etc/resolv.conf` pada web console klien dan ganti isinya dengan yang sudah di-copy tadi. Terakhir, untuk mengecek apakah klien sudah terhubung dengan jaringan luar, ketikkan command `ping google.com` pada web console klien.

<img src="https://github.com/immanuelmtpardede/Jarkom-Modul-2-E08-2022/blob/main/img/1.1.png" width=50%>

## No. 2
### Soal
Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.yyy.com dengan alias www.wise.yyy.com pada folder wise.
### Jawaban

## No. 3
### Soal
Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden.
### Jawaban

## No. 4
### Soal
Buat juga reverse domain untuk domain utama.
### Jawaban

## No. 5
### Soal
Agar dapat tetap dihubungi jika server WISE bermasalah, buatlah juga Berlint sebagai DNS Slave untuk domain utama.
### Jawaban

## No. 6
### Soal
Karena banyak informasi dari Handler, buatlah subdomain yang khusus untuk operation yaitu operation.wise.yyy.com dengan alias www.operation.wise.yyy.com yang didelegasikan dari WISE ke Berlint dengan IP menuju ke Eden dalam folder operation.
### Jawaban

## No. 7
### Soal
Untuk informasi yang lebih spesifik mengenai Operation Strix, buatlah subdomain melalui Berlint dengan akses strix.operation.wise.yyy.com dengan alias www.strix.operation.wise.yyy.com yang mengarah ke Eden.
### Jawaban

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
