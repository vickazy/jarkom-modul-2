# 1. DNS (Domain Name System)
DNS (_Domain Name System_) adalah sistem penamaan untuk semua device(smartphone, computer, atau
network) yang terhubung dengan internet. DNS Server berfungsi menerjemahkan nama domain menjadi alamat IP.

### 1.1 Instalasi bind dan dnsutils
Yang akan dijadikan DNS Server adalah KLAMPIS. Maka install bind pada KLAMPIS. Sebelum itu, pastikan sudah menjalankan perintah __apt-get update__.

![Klampis1](images/01.png)

Kemudian ketikkan __apt-get install bind9__ pada KLAMPIS

![Klampis2](images/02.png)

Kemudian install dnsutils pada __NGAGEL__ dan __NGINDEN__ dengan mengetikkan __apt-get install dnsutils__

### 1.2 Pembuatan Domain
Untuk membuat domain klampis.com, lakukan perintah __nano /etc/bind/named.conf.local__. Isikan seperti berikut:

![Klampis3](images/03.png)

Buat folder __klampis__ di dalam __/etc/bind__ 

![Klampis4](images/04.png)

Copykan file __db.local__ ke dalam folder klampis yang baru saja dibuat dan ubah namanya menjadi __klampis.com__

![Klampis5](images/05.png)

Kemudian buka file __klampis/klampis.com__ dan edit seperti berikut dengan IP KLAMPIS masing-masing kelompok:

![Klampis6](images/06.png)

Restart bind9 dengan perintah __service bind9 restart__

### 1.3 Setting nameserver pada client
Pada NGAGEL dan NGINDEN arahkan nameserver menuju IP KLAMPIS dengan mengedit file _resolv.conf_ dengan mengetikkan perintah __nano /etc/resolv.conf__

![Klampis7](images/07.png)

Untuk mencoba koneksi DNS, lakukan ping domain klampis.com dengan melakukan __ping klampis.com__ pada NGAGEL dan NGINDEN

![Klampis8](images/08.png)

### 1.4 Reverse DNS (Record PTR)
Reverse DNS atau Record PTR digunakan untuk menerjemahkan alamat IP ke alamat domain yang sudah diterjemahkan sebelumnya. Edit file __/etc/bind/named/conf.local__ pada KLAMPIS

![Klampis9](images/09.png)

Copy file db.local ke folder klampis dan ubah namanya menjadi __83.151.10.in-addr.arpa__

![Klampis10](images/10.png)

Edit file __83.151.10.in-addr.arpa__

![Klampis11](images/11.png)

Kemudian restart bind9 dengan perintah __service bind9 restart__

Untuk mengecek lakukan perintah __nslookup__ atau __dig__

![Klampis12](images/12.png)

### 1.5 Record CNAME
Record CNAME adalah sebuah record yang membuat alias name dan mengarahkan domain ke alamat/domain yang lain.

Buka file klampis.com pada KLAMPIS dan tambahkan syntax berikut:

![Klampis13](images/13.png)

Kemudian restart bind9 dengan perintah __service bind9 restart__

Lalu cek dengan melakukan __ping www.klampis.com__ dan __ping klampis.com__ keduanya akan mengarah ke host dengan IP yang sama.

![Klampis14](images/14.png)

![Klampis15](images/15.png)

### 1.6 Membuat DNS Slave
DNS Slave adalah DNS cadangan yang akan diakses jika server DNS utama mengalami kegagalan. Lakukan __apt-get update__ kemudian install bind9 di PUCANG dengan perintah __apt-get install bind9__

Edit file __/etc/bind/named.conf.local__ pada KLAMPIS dan tambahkan syntax berikut:

![Klampis16](images/16.png)

Kemudian buka file __/etc/bind/named.conf.local__ pada PUCANG dan tambahkan syntax berikut:

![Klampis17](images/17.png)

Apabila terjadi kegagalan pada DNS Server KLAMPIS, maka DNS Server akan dialihkan ke Server PUCANG. Ubah nameserver client yang tersambung dengan KLAMPIS (NGINDEN dan NGAGEL) dengan mengedit file __etc/resolv.conf__ menjadi IP PUCANG

![Klampis18](images/18.png)

### 1.7 Membuat Subdomain
Subdomain adalah bagian dari sebuah nama domain induk. Subdomain umumnya mengacu ke suatu alamat fisik di sebuah situs contohnya: __klampis.com__ merupakan sebuah domain induk. Sedangkan __test.klampis.com__ merupakan sebuah subdomain.

Edit file __/etc/bind/klampis/klampis.com__ lalu tambahkan subdomain untuk klampis.com yang mengarah ke IP KLAMPIS.

![Klampis19](images/19.png)

### 1.8 Delegasi Subdomain
Delegasi subdomain adalah pemberian wewenang atas sebuah subdomain kepada DNS baru.

Pada KLAMPIS, edit file __/etc/bind/klampis/klampis.com__ dan ubah menjadi seperti di bawah ini sesuai dengan pembagian IP KLAMPIS kelompok masing-masing.

![Klampis20](images/20.png)

Kemudian comment __dnssec-validation auto;__ dan tambahkan baris berikut pada __/etc/bind/named.conf.options__

![Klampis21](images/21.png)

Kemudian edit file __/etc/bind/named.conf.local__ menjadi seperti gambar di bawah:

![Klampis22](images/22.png)

Setelah itu restart dengan menjalankan __service bind9 restart__

Pada PUCANG, comment __dnssec-validation auto;__ dan tambahkan baris berikut pada __/etc/bind/named.conf.options__

![Klampis23](images/23.png)

Kemudian edit file __/etc/bind/named.conf.local__ menjadi seperti gambar di bawah:

![Klampis24](images/24.png)

Kemudian pada __/etc/bind/pucang/pucang.klampis.com__ ubah dan tambahkan record NS dan A untuk domain __pucang.klampis.com__ dan satu lagi record A untuk subdomain www.pucang.klampis.com yang mengarah ke PUCANG (sesuaikan dengan IP masing-masing)

![Klampis25](images/25.png)

Restart dengan menjalankan __service bind9 restart__

Setelah mendelegasikan zone pucang.klampis.com menuju __PUCANG__, kita dapat mengakses subdomain (www.pucang.klampis.com) yang ada pada pucang.klampis.com dengan menggunakan nameserver __KLAMPIS__ maupun __PUCANG__ dengan cara ping www.pucang.klampis.com pada client (__NGAGEL__ dan __NGINDEN__)

![Klampis26](images/26.png)