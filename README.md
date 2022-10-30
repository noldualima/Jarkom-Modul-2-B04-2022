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
nano /etc/bind/wise/wise.b07.com
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
[3 1](https://user-images.githubusercontent.com/72547769/198888967-b020bda5-3946-47c5-a05d-eaadd8d5c16f.png)
![3 2](https://user-images.githubusercontent.com/72547769/198888981-cbcd33b1-10c1-4cd6-9279-5faac472b3e7.png)

### Soal 4!
Buat juga reverse domain untuk domain utama

#### Jawab
Pada node wise lakukan perintah sebagai berikut :


### Soal 5


### Soal 6


### Soal 7


### Soal 8
```
apt-get install apache2 -y
service apache2 start
apt-get install php -y
apt-get install libapache2-mod-php7.0 -y
service apache2 
apt-get install ca-certificates openssl -y
apt-get install unzip -y
apt-get install git -y
git clone https://github.com/51Shades/Praktikum-Modul-2-Jarkom.git
unzip -o /root/Praktikum-Modul-2-Jarkom/\*.zip -d /root/Praktikum-Modul-2-Jarkom
echo "
<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/wise.b04.com
        ServerName wise.b04.com
        ServerAlias www.wise.b04.com

        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
" > /etc/apache2/sites-available/wise.b04.com.conf
a2ensite wise.b04.com
mkdir /var/www/wise.b04.com
cp -r /root/Praktikum-Modul-2-Jarkom/wise/. /var/www/wise.b04.com
service apache2 restart
```

### Soal 9
```
a2enmod rewrite
service apache2 restart
echo "
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule (.*) /index.php/\$1 [L]
" >/var/www/wise.b04.com/.htaccess
echo "
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/wise.b04.com
        ServerName wise.b04.com
        ServerAlias www.wise.b04.com

        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined

        <Directory /var/www/wise.b04.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/wise.b04.com.conf
service apache2 restart
```

### Soal 10
```
echo "
<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.b04.com
        ServerName eden.wise.b04.com
        ServerAlias www.eden.wise.b04.com

        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined

        <Directory /var/www/wise.b04.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/eden.wise.b04.com.conf
a2ensite eden.wise.b04.com
mkdir /var/www/eden.wise.b04.com
cp -r /root/Praktikum-Modul-2-Jarkom/eden.wise/. /var/www/eden.wise.b04.com
service apache2 restart
echo "<?php echo 'yes nomor 10' ?>" > /var/www/eden.wise.b04.com/index.php
```

### Soal 11
```
echo "
<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.b04.com
        ServerName eden.wise.b04.com
        ServerAlias www.eden.wise.b04.com

        <Directory /var/www/eden.wise.b04.com/public>
                Options +Indexes
        </Directory>

        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined

        <Directory /var/www/wise.b04.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/eden.wise.b04.com.conf
service apache2 restart
```

### Soal 12
```
echo "
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

        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined

        <Directory /var/www/wise.b04.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/eden.wise.b04.com.conf
service apache2 restart
```

### Soal 13
```
echo "
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

        Alias \"/js\" \"/var/www/eden.wise.b04.com/public/js\"


        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined

        <Directory /var/www/wise.b04.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/eden.wise.b04.com.conf
service apache2 restart
```

### Soal 14
```
echo "
<VirtualHost *:15000>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/strix.operation.wise.b04.com
        ServerName strix.operation.wise.b04.com
        ServerAlias www.strix.operation.wise.b04.com


        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
<VirtualHost *:15500>        
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/strix.operation.wise.b04.com
        ServerName strix.operation.wise.b04.com
        ServerAlias www.strix.operation.wise.b04.com
        

        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
" > /etc/apache2/sites-available/strix.operation.wise.b04.com.conf
a2ensite strix.operation.wise.b04.com
service apache2 restart
mkdir /var/www/strix.operation.wise.b04.com
cp -r /root/Praktikum-Modul-2-Jarkom/strix.operation.wise/ ./var/www/strix.operation.wise.b04.com/
echo "
<?php
        echo 'selamat 14';
?>
" > /var/www/strix.operation.wise.b04.com/index.php
echo "
# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf

Listen 80
Listen 15000
Listen 15500
<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>
" > /etc/apache2/ports.conf

service apache2 restart
```

### Soal 15
```
htpasswd -c -b /etc/apache2/.htpasswd Twilight opStrix

echo "
<VirtualHost *:15000>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/strix.operation.wise.b04.com
        ServerName strix.operation.wise.b04.com
        ServerAlias www.strix.operation.wise.b04.com

        <Directory \"/var/www/strix.operation.wise.b04.com\">
                AuthType Basic
                AuthName \"Restricted Content\"
                AuthUserFile /etc/apache2/.htpasswd
                Require valid-user
        </Directory>

        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
<VirtualHost *:15500>        
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/strix.operation.wise.b04.com
        ServerName strix.operation.wise.b04.com
        ServerAlias www.strix.operation.wise.b04.com
        
        <Directory \"/var/www/strix.operation.wise.b04.com\">
                AuthType Basic
                AuthName \"Restricted Content\"
                AuthUserFile /etc/apache2/.htpasswd
                Require valid-user
        </Directory>
        
        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
" > /etc/apache2/sites-available/strix.operation.wise.b04.com.conf
service apache2 restart
```

### Soal 16
```
echo "
<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        RewriteEngine On
        RewriteCond %{HTTP_HOST} !^wise.b04.com$
        RewriteRule /.* http://wise.b04.com/ [R]

        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
" > /etc/apache2/sites-available/000-default.conf
service apache2 restart
```

### Soal 17
```
echo "
RewriteEngine On
RewriteCond %{REQUEST_URI} !/public/images/eden.png
RewriteRule /.* http://eden.wise.b04.com/public/images/eden.png [L]
" > /var/www/eden.wise.b04.com/.htaccess

echo "
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

        Alias \"/js\" \"/var/www/eden.wise.b04.com/public/js\"

        <Directory /var/www/eden.wise.b04.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined

        <Directory /var/www/wise.b04.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/eden.wise.b04.com.conf
service apache2 restart

}

if [ $HOSTNAME == "Ostania" ]
then
    Ostania
elif [ $HOSTNAME == "WISE" ]
then
    Wise
elif [ $HOSTNAME == "Berlint" ]
then
    Berlint
elif [ $HOSTNAME == "SSS" ]
then
    SSS
elif [ $HOSTNAME == "Garden" ]
then
    Garden
elif [ $HOSTNAME == "Eden" ]
then
    Eden
fi
```

## Kendala

