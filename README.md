# Jarkom-Modul-5-D21-2023

## Kelompok D21
Akbar Putra Asenti P. 5025211004

Farrela Ranku Mahhisa 5025211129


## Set Up Topologi

Berikut ini pembagian rute subnet yang kami buat

![image](https://github.com/barpeot/Jarkom-Modul-5-D21-2023/assets/114351382/fbdcac9f-a124-47c0-b813-c34fe9600b51)

Kemudian, kita bagi menggunakan VLSM

(gambar tree vlsm)

Dari sini, kita dapatkan pembagian ip seperti pada gambar berikut

![image](https://github.com/barpeot/Jarkom-Modul-5-D21-2023/assets/114351382/ccc6b1fd-209b-46ab-88ba-862da5836b4e)

Setelah itu, kita assign ip berdasarkan pembagian berikut

![image](https://github.com/barpeot/Jarkom-Modul-5-D21-2023/assets/114351382/40a92d69-40bd-44cd-81e1-36c577058026)


## Nomor 1

```
iptables -t nat -A POSTROUTING -o eth0 -j SNAT -s 10.32.0.0/20 --to-source 192.168.122.2
```

`-t nat` Menentukan tabel yang akan dimanipulasi, dalam hal ini tabel NAT.

`-A POSTROUTING` Menambahkan aturan pada chain POSTROUTING. Chain ini berperan dalam pemrosesan paket setelah melewati routing decision dan sebelum meninggalkan sistem.

` --to-source 192.168.122.2` Menentukan alamat IP yang akan digunakan sebagai alamat sumber baru (setelah dilakukan SNAT). Dalam hal ini, alamat sumber paket akan diubah menjadi 192.168.122.2.

`-o eth0` Menentukan interface keluar yang akan dikenakan aturan, dalam hal ini eth0. Ini berarti aturan ini hanya akan berlaku untuk paket yang akan keluar melalui interface eth0.

`-j SNAT` Menentukan target dari aturan ini, dalam hal ini SNAT (Source NAT). SNAT digunakan untuk mengubah alamat sumber paket.

`-s 10.32.0.0/20` Menentukan range alamat sumber yang akan dikenakan aturan. Aturan ini akan berlaku untuk paket yang berasal dari alamat IP dalam range 10.32.0.0 dengan netmask 20.

## Nomor 2

```
iptables -A INPUT -p udp -j DROP
iptables -A INPUT -p tcp ! --dport 8080 -j DROP
```

`-A INPUT` menambahkan rules pada chain untuk mengecek paket yang masuk

`-p udp dan -p tcp` digunakan untuk menunjukkan protokol spesifik yang ingin kita buatkan rulesnya

`! --dport 8080` digunakan untuk memastikan rules akan berlaku untuk semua port yang bukan 8080

`-j DROP` akan mengeksekusi drop saat syarat memenuhi

## Nomor 3

```
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

`-m state --state ESTABLISHED, RELATED` berfungsi menggunakan modul state untuk mengatur aturan berdasarkan status koneksi yang akan mengizinkan paket yang terkait dengan koneksi yang sudah ada (ESTABLISHED) atau terkait dengan koneksi yang sedang dibuat (RELATED)

`-p icmp` menarget protokol icmp

`-m connlimit --connlimit-above 3 --connlimit-mask 0` berfungsi menggunakan modul connlimit untuk membatasi jumlah koneksi ICMP yang diizinkan. Rules ini akan menjatuhkan drop paket ICMP jika jumlah koneksi melebihi 3

## Nomor 4

```
iptables -A INPUT -p tcp --dport 22 ! -s 10.32.8.0/22
```

`-A INPUT` menambahkan rules pada chain untuk mengecek paket yang masuk

`-p tcp` menggunakan protokol tcp

`--dport 22` rule berlaku pada destination port 22, yaitu default port dari SSH.

`! -s 10.32.8.0/22 -j REJECT` rule berlaku untuk selain subnet 10.32.8.0 dengan netmask 22 maka akan direject

## Nomor 5

```
iptables -A INPUT -m time --timestart 16:00 --timestop 8:00 -j DROP
iptables -A INPUT -m time --weekdays 6,7 -j DROP
```

`-m time --timestart 16:00 --timestop 8:00 -j DROP` menggunakan modul time, untuk membuat aturan yang akan melakukan drop paket yang berlaku dari jam 16.00 hinga 8.00. 

` -m time --weekdays 6,7 -j DROP` menggunakan modul time untuk membuat aturan yang akan melakukan drop paket saat hari Sabtu dan Minggu

## Nomor 6

```
iptables -A INPUT -m time --weekdays 1,2,3,4 --timestart 12:00 --timestop 13:00 -j DROP
iptables -A INPUT -m time --weekdays 5 --timestart 11:00 --timestop 13:00 -j DROP
```

`-m time --weekdays 1,2,3,4 --timestart 12:00 --timestop 13:00 -j DROP` menggunakan modul time untuk membuat aturan yang akan melakukan drop paket yang berlaku saat hari Senin, Selasa, Rabu, Kamis dari jam 12.00 hingga 13.00

`-m time --weekdays 5 --timestart 11:00 --timestop 13:00 -j DROP` menggunakan modul time untuk membuat aturan yang akan melakukan drop paket saat hari Jumat dari jam 11.00 hingga 13.00


## Nomor 7

Di Heiter (Konfigurasi untuk Sein)
```
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 10.32.8.2 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.32.14.134:80
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 10.32.8.2 -m statistic --mode nth --every 1 --packet 0 -j DNAT --to-destination 10.32.8.2:80
```

`--dport 80` Menentukan port destinasi yang akan dikenakan aturan, yaitu port 80 (HTTP).

`-d 10.32.8.2` Menentukan alamat tujuan yang akan dikenakan aturan, yaitu 10.32.8.2.

`-m statistic --mode nth --every 2 --packet 0` Menggunakan modul statistik untuk melakukan DNAT setiap paket kedua. Ini berarti setiap kedua paket yang menuju ke alamat IP 10.32.8.2 dan port 80 akan diarahkan ke alamat IP 10.32.14.134 dan port 80.

`-m statistic --mode nth --every 1 --packet 0` Sama seperti aturan pertama, hanya berbeda pada parameter --every 1, yang berarti setiap paket pertama yang menuju ke alamat IP 10.32.8.2 dan port 80 akan diarahkan kembali ke alamat IP 10.32.8.2 dan port 80 sendiri.

`-j DNAT --to-destination 10.32.14.134:80` Menentukan target aturan, yaitu DNAT (Destination NAT), dan mengarahkan paket ke alamat tujuan yang baru, yaitu 10.32.14.134 (Stark) dan port 80.

Karena urutan input rule dalam iptables, maka rule yang pertama akan dicek terlebih dahulu, sehingga 

Di Frieren (Konfigurasi untuk Stark)
```
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 10.32.14.134 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.32.8.2:443
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 10.32.14.134 -m statistic --mode nth --every 1 --packet 0 -j DNAT --to-destination 10.32.14.134:443
```

Di Frieren konfigurasinya sama dengan konfigurasi Sein, bedanya menggunakan port 443, yang merupakan default port dari HTTPS.

## Nomor 8

```
iptables -A INPUT -s 10.32.14.144/30  -m time --datestop 2024-3-20 -j DROP
```

`-A INPUT` Menambahkan aturan pada chain INPUT, yang berperan dalam pemrosesan paket yang ditujukan ke sistem lokal.

`-s 10.32.14.144/30` Menentukan range alamat sumber yang akan dikenakan aturan. Aturan ini akan berlaku untuk paket-paket yang berasal dari alamat IP dalam range 10.32.14.144 hingga 10.32.14.147 (subnet dengan prefix length /30).

`-m time --datestop 2024-3-20` Menggunakan modul waktu untuk menentukan periode waktu saat aturan ini berlaku. Dalam hal ini, aturan ini akan berlaku hingga tanggal 20 Maret 2024.

`-j DROP` Menentukan tindakan yang akan diambil jika paket memenuhi kriteria aturan. Dalam hal ini, paket-paket yang berasal dari alamat IP dalam range 10.32.14.144/30 akan ditolak (DROP) jika waktu saat ini berada dalam rentang waktu yang ditentukan.

## Nomor 9

```
iptables -N portscan
iptables -A INPUT -m recent --name portscan --update --seconds 600 --hitcount 20 -j DROP
iptables -A FORWARD -m recent --name portscan --update --seconds 600 --hitcount 20 -j DROP
iptables -A INPUT -m recent --name portscan --set -j ACCEPT
iptables -A FORWARD -m recent --name portscan --set -j ACCEPT
```

Jika terdapat lebih dari 20 paket dalam waktu 600 detik (10 menit) menuju port yang sama, paket-paket tersebut akan ditolak (DROP).

Untuk testing sendiri, kita bisa melakukan ping ke Webserver, pada paket ke-21 akan langsung ditolak

![image](https://github.com/barpeot/Jarkom-Modul-5-D21-2023/assets/114351382/82b47d20-295d-449c-9a63-268a780c5e24)


## Nomor 10

```
iptables -N LOGGING
iptables -A INPUT -j LOGGING
iptables -A OUTPUT -j LOGGING
iptables -A LOGGING -m limit --limit 2/min -j LOG --log-prefix "IPTables-Dropped: " --log-level 4
iptables -A INPUT -j LOG --log-prefix "Paket didrop" --log-level 4
```

`iptables -N LOGGING` Membuat chain baru bernama LOGGING

`iptables -A INPUT -j LOGGING` Menambahkan aturan pada chain INPUT untuk mengarahkan paket yang masuk ke chain LOGGING. Artinya, setiap paket yang mencapai chain INPUT akan diarahkan ke chain LOGGING.

`iptables -A OUTPUT -j LOGGING` Menambahkan aturan pada chain OUTPUT untuk mengarahkan paket yang keluar ke chain LOGGING. Ini berarti setiap paket yang keluar akan diarahkan ke chain LOGGING.

`iptables -A LOGGING -m limit --limit 2/min -j LOG --log-prefix "IPTables-Dropped: " --log-level 4` Menambahkan aturan pada chain LOGGING untuk melakukan logging terhadap paket yang melewati chain ini. Aturan ini menggunakan modul limit untuk membatasi jumlah log yang dihasilkan (2 log per menit). Prefix "IPTables-Dropped" ditambahkan ke setiap pesan log, dan level log ditetapkan pada 4 (WARNING).

`iptables -A INPUT -j LOG --log-prefix "Paket didrop" --log-level 4` Menambahkan aturan pada chain INPUT untuk melakukan logging terhadap paket yang ditolak (dropped). Setiap paket yang ditolak akan di-log dengan pesan "Paket didrop" dan level log 4 (WARNING).

```
touch /var/log/iptables.log
echo 'kern.warning      /var/log/iptables.log ' >> /etc/rsyslog.conf

service rsyslog restart
```

Selanjutnya perlu menambahkan konfigurasi pada file /etc/rsyslog.conf untuk merekam pesan log iptables ke berkas log /var/log/iptables.log kemudian restart layanan rsyslog untuk menerapkan konfigurasi baru.
