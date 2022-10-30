# Jarkom-Modul-2-B04-2022

## Praktikum Jaringan Komputer Modul 2 - DNS dan Web Server
NRP | Nama
-----------|---------------------------
5025201025 | Dawamul Fikri Aqil
5025201048 | Afril Muzaqqi Arif
5025201165 | Gabriel Solomon Sitanggang
---------------------------------------

## Soal Shift Modul 2
Link : https://docs.google.com/document/d/11Mz2Fd3DKtGkCknHee9VZRdJYvZ3YAMIaifObHEpBFo/edit

## Topologi
file .gns3project dapat dilihat di link [berikut](https://github.com/noldualima/Jarkom-Modul-2-B04-2022/blob/main/Praktikum2.gns3project).

![messageImage_1666696807418](https://user-images.githubusercontent.com/91501217/197770142-e39e2b78-89a5-4ba0-b5c0-40472f52d442.jpg)

WISE akan dijadikan sebagai DNS Master, Berlint akan dijadikan DNS Slave, dan Eden akan digunakan sebagai Web Server. Terdapat 2 Client yaitu SSS, dan Garden

## Penjelasan :
### Soal 1
Pembuatan topologi seperti file yang sudah dilampirkan dan menggunakan konfigurasi pada tiap nodenya seperti berikut:
(DNS Master) Wise
network configuration
```
auto eth0
iface eth0 inet static
	address 10.5.1.2
  netmask 255.255.255.0
	gateway 10.5.1.1
```
(DNS Slave) Berlint
```
auto eth0
iface eth0 inet static
	address 10.5.3.2
	netmask 255.255.255.0
	gateway 10.5.3.1
```
(Web Server) Eden
```
auto eth0
iface eth0 inet static
	address 10.5.3.3
	netmask 255.255.255.0
	gateway 10.5.3.1
```
(Client) SSS
```
auto eth0
iface eth0 inet static
	address 10.5.2.2
	netmask 255.255.255.0
	gateway 10.5.2.1
```
(Client) Garden
```
auto eth0
iface eth0 inet static
	address 10.5.2.3
	netmask 255.255.255.0
	gateway 10.5.2.1
```
Semua node terhubung pada router Ostania, sehingga dapat mengakses internet

![ping1](https://user-images.githubusercontent.com/91501217/197776672-93545b2f-9612-465d-b9ca-43c5f834b0df.png)
![ping2](https://user-images.githubusercontent.com/91501217/197776682-491c33ba-03ee-4ef1-9901-8804ec3acb19.png)
![ping3](https://user-images.githubusercontent.com/91501217/197776691-1b85b0e5-5a17-4f60-aa7c-e7af96525fa9.png)
![ping4](https://user-images.githubusercontent.com/91501217/197776698-e4c5ae62-0bbc-4a7f-8577-3982b280e604.png)
![ping5](https://user-images.githubusercontent.com/91501217/197776704-3a6d0951-48db-4b79-975b-f5832a38fe29.png)


### Soal 2
Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.yyy.com dengan alias www.wise.yyy.com pada folder wise

![namedconflocal](https://user-images.githubusercontent.com/91501217/197779905-f3c662b4-22ed-4c82-ace3-ff85a3800efb.png)
setelah menginstall bind9, lalu buka file named.conf.local lalu isi seperti gambar yang diatas

![folderwise](https://user-images.githubusercontent.com/91501217/197780266-e3abdfe8-cb77-4eea-a235-0454f5e9c307.png)
Penjelasan Bantuin bikin penjelasan 

![testcname](https://user-images.githubusercontent.com/91501217/197782993-6598ee64-8805-4f1b-a5d2-a11f4404e0b4.png)
Lalu menggunakan host -t CNAME www.wise.b04.com akan muncul seperti gambar berikut

### Soal 3
Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden

#### Jawab
Pada node wise lakukan perintah sebagai berikut :
- edit file `wise.b04.com` pada folder `/etc/bind/wise` dengan cara :
```
nano /etc/bind/wise/wise.b04.com
```
- tambahkan konfigurasi subdomain pada file `wise.b04.com`, sehingga menjadi seperti berikut
```
$TTL    604800
@       IN      SOA     wise.b04.com. root.wise.b04.com. (
                                2       ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
@               IN      NS      wise.b04.com.
@               IN      A       10.5.3.3 ; IP Eden
www             IN      CNAME   wise.b04.com.
eden           IN      A       10.5.3.3 ; IP Eden
www.eden       IN      CNAME   eden.wise.b04.com.
ns1             IN      A       10.5.3.2; IP Berlint
operation           IN      NS      ns1
```
- lakukan restart server **bind9** dengan syntax berikut:
```
service bind9 restart
```
   atau restart dengan perintah berikut:
```
named -g
```
- untuk mencoba koneksi subdomain, lakukan ping pada domain yang sudah dibuat dengan:
```
ping eden.wise.b04.com
```
- client berhasil terkoneksi dengan sub domain jika response ping seperti tampilan berikut:
![3 1](https://user-images.githubusercontent.com/72547769/198888967-b020bda5-3946-47c5-a05d-eaadd8d5c16f.png)
![3 2](https://user-images.githubusercontent.com/72547769/198888981-cbcd33b1-10c1-4cd6-9279-5faac472b3e7.png)

### Soal 4
Buat juga reverse domain untuk domain utama

#### Jawab
Pada node wise lakukan perintah sebagai berikut :
- edit file `named.conf.local` pada folder `/etc/bind/` dengan cara:
```
nano /etc/bind/named.conf.local
```
- tambahkan konfigurasi reserve domain sehingga pada file `named.conf.local`, sehingga seperti berikut
```
zone "wise.b04.com" {
        type master;
        //notify yes;
        //also-notify {10.5.3.2;};  Masukan IP Berlint tanpa tanda petik
        file "/etc/bind/wise/wise.b04.com";
        allow-transfer {10.5.3.2;}; // Masukan IP Berlint tanpa tanda petik
};

zone "2.5.10.in-addr.arpa" {
        type master;
        file "/etc/bind/wise/2.5.10.in-addr.arpa";
};
```
- Copykan file db.local pada path /etc/bind ke dalam folder wise yang baru saja dibuat dan ubah namanya menjadi 2.5.10.in-addr.arpa
- edit file `2.5.10.in-addr.arpa` dengan menggunakan perintah:
```
nano /etc/bind/wise/2.5.10.in-addr.arpa
```
- Sesuaikan konfigurasi sehingga menjadi seperti berikut :
```
$TTL    604800
@       IN      SOA     wise.b04.com. root.wise.b04.com. (
                                2       ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
2.5.10.in-addr.arpa.   IN      NS      wise.b04.com.
2                       IN      PTR     wise.b04.com.
```
- lakukan restart server **bind9** dengan syntax berikut:
```
service bind9 restart
```
   atau restart dengan perintah berikut:
```
named -g
```
- untuk mencoba konfigurasi, lakukan install `dnsutils` pastikan name server merupakan IP dari ostania dengan:
```
apt-get update
apt-get install dnsutils
```
- kembalikan nameserver pada `/etc/resolv.conf` dengan IP wise, dan lakukan :
```
host -t PTR "10.5.2.2"
```
- konfigurasi berhasil jika ada tampilan sebagai berikut:
![4 1](https://user-images.githubusercontent.com/72547769/198890360-aa6c1948-8018-4cb6-a140-88610fd7670e.png)

### Soal 5
Agar dapat tetap dihubungi jika server WISE bermasalah, buatlah juga Berlint sebagai DNS Slave untuk domain utama
#### Jawab
Pada node wise lakukan perintah sebagai berikut:
- edit `named.conf.local` pada folder `/etc/bind/` dengan cara :
```
nano /etc/bind/named.conf.local
```
- tambahkan konfigurasi reserve domain sehingga pada file `named.conf.local` sehingga terlihat sebagai berikut:
```
zone "wise.b04.com" {
        type master;
        //notify yes;
        //also-notify {10.5.3.2;};  Masukan IP Berlint tanpa tanda petik
        file "/etc/bind/wise/wise.b04.com";
        allow-transfer {10.5.3.2;}; // Masukan IP Berlint tanpa tanda petik
};

zone "2.5.10.in-addr.arpa" {
        type master;
        file "/etc/bind/wise/2.5.10.in-addr.arpa";
};
```
- lakukan restart server **bind9** dengan syntax berikut:
```
service bind9 restart
```
   atau restart dengan perintah berikut:
```
named -g
```
Pada node Berlint lakukan perintah sebagai berikut :
- terlebih dahulu lakukan update package lists dengan menjalankan command:
```
apt-get update
```
- lakukan update silahkan install aplikasi bind9 pada Berlint dengan perintah:
```
apt-get install bind9 -y
```
- edit file /etc/bind/named.conf.local dengan menggunakan :
```
nano /etc/bind/named.conf.local
```
- tambahkan syntax berikut:
```
zone "operation.wise.b04.com"{
        type master;
        file "/etc/bind/operation/operation.wise.b04.com";
};
```
- lakukan restart server **bind9** dengan syntax berikut:
```
service bind9 restart
```
   atau restart dengan perintah berikut:
```
named -g
```
Proses testing dapat dilakukan dengan mematikan server bind9 pada wise dengan sytax :
```
service bind9 stop
```
pada client (SSS dan Garden) lakukan hal berikut :
- edit file seolv.conf dan tambahkan `IP berlint` Sehingga terlihat seperti berikut :
```
nameserver 10.5.1.2 #ip Wise
nameserver 10.5.3.2 #ip Berlint
```
- client berhasil terkoneksi jika response ping seperti tampilan berikut :
![5 2](https://user-images.githubusercontent.com/72547769/198891960-9c9b856b-f247-4f2e-bb10-08ea147339c5.png)

### Soal 6
Karena banyak informasi dari Handler, buatlah subdomain yang khusus untuk operation yaitu operation.wise.yyy.com dengan alias www.operation.wise.yyy.com yang didelegasikan dari WISE ke Berlint dengan IP menuju ke Eden dalam folder operation

#### Jawab

Pada node Wise lakukan perintah sebagai berikut :

- edit file /etc/bind/wise/wise.b04.com dengan menggunakan :
```
nano /etc/bind/wise/wise.b04.com
```
- sesuaikan konfigurasi sehingga tampak sebagai berikut :
```
ns1                 IN      A       10.5.3.2; IP Berlint
operation           IN      NS      ns1
```
- edit file /etc/bind/named.conf.options dengan menggunakan :
```
nano /etc/bind/named.conf.options
```
- sesuaikan konfigurasi sehingga tampak sebagai berikut :
```
options {
        directory "/var/cache/bind";

        allow-query{any;};
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```
Pada node Berlint lakukan perintah sebagai berikut :

- edit file /etc/bind/operation/operation.wise.b04.com dengan menggunakan :
```
nano /etc/bind/operation/operation.wise.b04.com
```
- sesuaikan konfigurasi sehingga tampak sebagai berikut :
```
$TTL    604800
@       IN      SOA     operation.wise.b04.com. root.operation.wise.b04.com. (
                                2      ; Serial
                        604800         ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@               IN      NS      operation.wise.b04.com.
@               IN      A       10.5.3.3       ;ip Eden
www             IN      CNAME   operation.wise.b04.com.
strix           IN      A       10.5.3.3       ;IP Eden
www.strix       IN      CNAME   strix.operation.wise.b04.com.
```
- edit file /etc/bind/named.conf.options dengan menggunakan :
```
nano /etc/bind/named.conf.options
```
- sesuaikan konfigurasi sehingga tampak sebagai berikut :
```
options {
        directory "/var/cache/bind";
        allow-query{any;};
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```
- lakukan restart server **bind9** dengan syntax berikut:
```
service bind9 restart
```
   atau restart dengan perintah berikut:
```
named -g
```
untuk mengetahui apakah konfigurasi sudah benar, Pada node Client (SSS dan Garden) lakukan ping dengan sytax:
```
operation.wise.b04.com
```
konfigurasi dikatakan benar jika hasil ping sebagai berikut:
![6 1](https://user-images.githubusercontent.com/72547769/198891998-ba143968-fec9-4b05-af5f-17bc06f40408.png)

### Soal 7
Untuk informasi yang lebih spesifik mengenai Operation Strix, buatlah subdomain melalui Berlint dengan akses strix.operation.wise.yyy.com dengan alias www.strix.operation.wise.yyy.com yang mengarah ke Eden

#### Jawab

Pada node Berlint lakukan perintah sebagai berikut :

- edit file /etc/bind/operation/operation.wise.b04.com dengan menggunakan :
```
nano /etc/bind/operation/operation.wise.b04.com
```
- sesuaikan konfigurasi sehingga tampak sebagai berikut :
```
$TTL    604800
@       IN      SOA     operation.wise.b04.com. root.operation.wise.b04.com. (
                                2      ; Serial
                        604800         ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@               IN      NS      operation.wise.b04.com.
@               IN      A       10.5.3.3       ;ip Eden
www             IN      CNAME   operation.wise.b04.com.
strix           IN      A       10.5.3.3       ;IP Eden
www.strix       IN      CNAME   strix.operation.wise.b04.com.
```
- lakukan restart server **bind9** dengan syntax berikut:
```
service bind9 restart
```
   atau restart dengan perintah berikut:
```
named -g
```
untuk mengetahui apakah konfigurasi sudah benar, Pada node Client (SSS dan Garden) lakukan ping dengan sytax:
```
strix.operation.wise.b04.com
```
konfigurasi dikatakan benar jika hasil ping sebagai berikut :
![7 1](https://user-images.githubusercontent.com/72547769/198892138-a0f39ead-3d6e-42eb-b904-010d07bae775.png)


### Soal 8
Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.wise.yyy.com. Pertama, Loid membutuhkan webserver dengan DocumentRoot pada /var/www/wise.yyy.com

### Jawab

Pada node eden lakukan perintah sebagai berikut :
- lakukan instalasi apache2 dengan menggunakan syntax berikut:
```
apt-get install apache2 -y
```
- lakukan instalasi php dengan menggunakan syntax berikut:
```
apt-get install php -y
apt-get install libapache2-mod-php7.0
```
- aktifkan service apache2 dengan menggunakan syntax berikut
```
service apache2 start
```
edit file /etc/apache2/sites-available/wise.b04.com.conf dengan menggunakan perintah
```
nano /etc/apache2/sites-available/wise.b04.com.conf
```
sesuaikan konfigurasi sehingga tampak sebagai berikut :
```
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/wise.b04.com
        ServerName wise.b04.com
        ServerAlias www.wise.b04.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory /var/www/wise.b04.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
</VirtualHost>
```
untuk mengecek konfigurasi webserver sudah tepat dapat melakukan hal berikut pada node Client (SSS dan Garden) :

- lakukan instalasi dnsutils dengan menggunakan sytax berikut :
```
apt-get install dnsutils -y
```
- lakukan instalasi lynx dengan menggunakan
```
apt-get install lynx -y
```
- cek koneksi dengan menggunakan perintah
```
lynx  www.wise.b04.com
```
- konfigurasi dapat dikatakan benar jika keluaran sebagai berikut:
![8 1](https://user-images.githubusercontent.com/72547769/198892533-214c4917-4f6e-422c-97dd-42d8607febc5.png)

### Soal 9
Setelah itu, Loid juga membutuhkan agar url www.wise.yyy.com/index.php/home dapat menjadi menjadi www.wise.yyy.com/home

### Jawab
Pada node eden lakukan perintah sebagai berikut :
- edit file /etc/apache2/sites-available/wise.b04.com.conf dengan menggunakan perintah
```
nano /var/www/wise.b04.com/.htaccess
```
- sesuaikan konfigurasi sehingga tampak sebagai berikut
```
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule (.*) /index.php/$1 [L]
```
- edit file /etc/apache2/sites-available/wise.b04.com.conf dengan menggunakan perintah
```
nano /etc/apache2/sites-available/wise.b04.com.conf
```
- sesuaikan konfigurasi sehingga tampak sebagai berikut
```

<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/wise.b04.com
        ServerName wise.b04.com
        ServerAlias www.wise.b04.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory /var/www/wise.b04.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
</VirtualHost>
```
- lakukan restart service `apache2` dengan menggunakan syntax berikut :
```
service apache2 restart
```
untuk mengecek konfigurasi. pada node Client (SSS dan Garden) lakukan perintah sebagai berikut :
```
lynx www.wise.b07.com/index.php/home
```
konfigurasi dapat dikatakan benar jika keluaran sebagai berikut
![9 1](https://user-images.githubusercontent.com/72547769/198892723-6f4ddf55-e9d0-46c0-a51c-607788a1a4da.png)

### Soal 10, 11, 12, 13
Setelah itu, pada subdomain www.eden.wise.yyy.com, Loid membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/eden.wise.yyy.com. Akan tetapi, pada folder /public, Loid ingin hanya dapat melakukan directory listing saja. Tidak hanya itu, Loid juga ingin menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache. Loid juga meminta Franky untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.eden.wise.yyy.com/public/js menjadi www.eden.wise.yyy.com/js

### Jawab

Pada node eden lakukan perintah sebagai berikut :
- edit file /etc/apache2/sites-available/eden.wise.b04.com.confdengan menggunakan perintah
```
nano /etc/apache2/sites-available/eden.wise.b04.com.conf
```
- sesuaikan konfigurasi sehingga tampak sebagai berikut
```
<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.b04.com
        ServerName eden.wise.b04.com
        ServerAlias www.eden.wise.b04.com

        ErrorDocument 404 /error/404.html
        ErrorDocument 500 /error/404.html
        ErrorDocument 502 /error/404.html
        ErrorDocument 503 /error/404.html
        ErrorDocument 504 /error/404.html

        <Directory /var/www/eden.wise.b04.com/public>
                Options +Indexes
        </Directory>

        Alias "/js" "/var/www/eden.wise.b04.com/public/js"
        <Directory /var/www/eden.wise.b04.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory /var/www/wise.b04.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
</VirtualHost>
```
untuk mengecek konfigurasi, pada node client (SSS dan Garden) lakukan perintah sebagai berikut :

- cek folder yang sudah dibuat, dengan menggunakan
```
cd /var/www/wise.b04.com/
ls
```
- konfigurasi dapat dikatakan benar jika tampilan terlihat sebagai berikut :
![10 1](https://user-images.githubusercontent.com/72547769/198893044-3d3666bc-2b75-4b22-b185-42868e68114c.png)

- tampilan situs jika situs tidak ditemukan akan muncul sebagai berikut :
![10 2](https://user-images.githubusercontent.com/72547769/198893135-0a372a6b-a40e-4b49-93f3-b800fd5e8e71.png)

### Soal 14
Loid meminta agar www.strix.operation.wise.yyy.com hanya bisa diakses dengan port 15000 dan port 15500

### Jawab
pada eden buat config baru dengan `nano /etc/apache2/sites-available/strix.operation.wise.b04.com.conf`
![14 1](https://user-images.githubusercontent.com/72547769/198893268-dfc3c6f6-7dc5-43aa-bc96-13158ba5c2da.png)

kemudian aktifkan dengan `a2ensite strix.operation.wise.b04.com` kemudian edit juga pada `/etc/apache2/ports.conf` seperti berikut
![14 2](https://user-images.githubusercontent.com/72547769/198893367-93b5ecb7-f781-4ff2-90ee-1b7e11523c90.png)

Kemudian tes dengan `lynx strix.operation.wise.b07.com:15000`
![14 2 5](https://user-images.githubusercontent.com/72547769/198893948-1a028673-ed95-4cde-86b5-7a33d1326e98.png)

![14 3](https://user-images.githubusercontent.com/72547769/198893690-d46ed532-04cb-4c02-881c-10aebc871c09.png)

### Soal 15
dengan autentikasi username Twilight dan password opStrix dan file di /var/www/strix.operation.wise.yyy (15) dan setiap kali mengakses IP Eden akan dialihkan secara otomatis ke www.wise.yyy.com

### Jawab

Buat password di Eden untuk apache2 `htpasswd -c -b /etc/apache2/.htpasswd Twilight opStrix` dan tambahkan
```
<Directory \"/var/www/strix.operation.wise.b04.com\">
                AuthType Basic
                AuthName \"Restricted Content\"
                AuthUserFile /etc/apache2/.htpasswd
                Require valid-user
        </Directory>
```
pada setiap host lalu test dengan `lynx strix.operation.wise.b04.com:15000` maka akan diminta memasukan username dan password
![15 1](https://user-images.githubusercontent.com/72547769/198894059-f0b867bc-d1b8-4bb3-b090-cbd9dd9460c9.png)
dan hasilnya seperti ini
![15 2](https://user-images.githubusercontent.com/72547769/198894108-cd9784b5-a677-4007-a3f5-6b353306dd5a.png)


### Soal 16
dan setiap kali mengakses IP Eden akan dialihkan secara otomatis ke www.wise.yyy.com

### jawab
pada `/etc/apache2/sites-available/000-default.conf`
```
	RewriteEngine On
        RewriteCond %{HTTP_HOST} !^wise.b04.com$
        RewriteRule /.* http://wise.b04.com/ [R]
```
lalu tes menggunakan Ip Eden lynx 10.5.3.3 Maka hasilnya
![16 1](https://user-images.githubusercontent.com/72547769/198894324-81d92cf1-76eb-41db-ba45-6efd5eb38bd4.png)


### Soal 17
Karena website www.eden.wise.yyy.com semakin banyak pengunjung dan banyak modifikasi sehingga banyak gambar-gambar yang random, maka Loid ingin mengubah request gambar yang memiliki substring “eden” akan diarahkan menuju eden.png. Bantulah Agent Twilight dan Organisasi WISE menjaga perdamaian!

### Jawab

Pada node Eden lakukan perintah sebagai berikut :
```
nano /var/www/eden.wise.b04.com/.htaccess
```
sesuaikan konfigurasi sehingga tampak sebagai berikut :
```
RewriteEngine On
RewriteCond %{REQUEST_URI} !/public/images/eden.png
RewriteRule /.* http://eden.wise.b04.com/public/images/eden.png [L]
```
lalu ganti config pada 
```
/etc/apache2/sites-available/eden.wise.b07.com.conf
```
dengan menambah `AllowOverride All` lalu jangan lupa `a2enmod rewrite` dan juga jangan lupa `restart apache`
```
<Directory /var/www/eden.wise.b04.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
```
![17 1](https://user-images.githubusercontent.com/72547769/198894573-c499098c-7117-4186-86a0-12c509dcc4cd.png)

kemudian cek dengan
```
lynx www.eden.wise.b07.com/asedenash
```
![17 2](https://user-images.githubusercontent.com/72547769/198894866-f4eb3db5-0c7a-4b2f-a8cc-76757ca09649.png)

## Kendala
