# Laporan Resmi Praktikum Jaringan Komputer Modul 2

## Anggota Kelompok
- 5025201248 - Fajar Zuhri Hadiyanto - pengerjaan bagian web server
- 5025201047 - Sidrotul Munawaroh - pengerjaan bagian topologi, config ip, dan dns server

## Layout
![00  topologi](https://user-images.githubusercontent.com/52820619/198819430-816ac5cf-4827-4b1f-a61a-8b89ddaa56ce.png)

## IP Address
### Ostania
![00 a  ip ostania](https://user-images.githubusercontent.com/52820619/198819432-e244f45d-2987-41fd-bc95-e5555c42688c.png)

### Wise
![00 b  ip wise](https://user-images.githubusercontent.com/52820619/198819434-31379197-ed81-4200-8156-27bcf4c512af.png)

### Berlint
![00 c  ip berlint](https://user-images.githubusercontent.com/52820619/198819436-22f44bb5-c04e-4918-b508-dcab8285b6d4.png)

### Eden
![00 d  ip eden](https://user-images.githubusercontent.com/52820619/198819437-9ee66fe2-f09c-418c-beb2-4deaf24f3c7f.png)

### SSS
![00 e  ip sss](https://user-images.githubusercontent.com/52820619/198819438-6e55d1ec-4d49-46a8-aa45-5fdad4f28702.png)

### Garden
![00 f  ip garden](https://user-images.githubusercontent.com/52820619/198819440-f13c816a-34db-48ae-8301-aca75014cc4a.png)

## Pengerjaan
### Nomor 1
tambahkan line berikut
```
nameserver 192.193.2.2
nameserver 192.193.3.2
nameserver 192.168.122.1
```
pada `/etc/resolv.conf` untuk setiap node agar dapat terhubung ke ketiga dns server tersebut, untuk yang ip ketiga ditambahkan agar dapat terkoneksi ke internet

berikut ini hasilnya

![01 a  ostania ping](https://user-images.githubusercontent.com/52820619/198819441-e6200e09-3890-409c-b1c3-102d7571aef5.png)
![01 b  wise ping](https://user-images.githubusercontent.com/52820619/198819442-1a46f2dc-9deb-43e3-bcf5-3c8e29671ba7.png)
![01 c  berlint ping](https://user-images.githubusercontent.com/52820619/198819443-69898d8b-798c-4086-ba2b-afeeca489d8f.png)
![01 d  eden ping](https://user-images.githubusercontent.com/52820619/198819444-f014b318-6cf9-4b43-b6b7-42c5a8fb1be7.png)
![01 e  sss ping](https://user-images.githubusercontent.com/52820619/198819445-4770480b-6244-40a2-8f50-4c0bb6964cf9.png)
![01 f  garden ping](https://user-images.githubusercontent.com/52820619/198819446-7ec4f6b9-56c2-4c16-bb64-e89d41fa23d4.png)

### Nomor 2
Pada server wise, Setelah melakukan instalasi bind9 dengan perintah `apt-get install bind9 -y`. lakukan konfigurasi pada file `/ect/bind/named.conf.local` dan isi sebagai berikut
```
zone "wise.E02.com" {
    type master;
    file "/etc/bind/wise/wise.E02.com";
};
```

lalu buat file `/etc/bind/wise/wise.E02.com` dengan isi sebagai berikut
```
$TTL    604800
@       IN      SOA     wise.E02.com. root.wise.E02.com. (
                     2022102601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@         IN      NS      wise.E02.com.
@         IN      A       192.193.2.2
www       IN      CNAME   wise.E02.com.
@         IN      AAAA    ::1
```

lalu restart bind9 dengan `service bind9 restart`

Pada client SSS, lakukan ping ke wise.e02.com dan www.wise.e02.com. Lalu untuk melakukan pengecekan alias, ketikkan `host -t CNAME www.wise.e02.com`.

![02  domain wise E02 com](https://user-images.githubusercontent.com/52820619/198819448-0fac5f6a-b70a-4a65-a88e-e45628a8b805.png)

### Nomor 3

Sama halnya pada nomor2, tambahkan code berikut pada file `/etc/bind/named.conf.local`
```
zone "eden.wise.E02.com" {
    type master;
    file "/etc/bind/wise/eden.wise.E02.com";
};
```
lalu buat file `/etc/bind/wise/eden.wise.E02.com` dengan isi sebagai berikut
```
$TTL    604800
@       IN      SOA     eden.wise.E02.com. eden.root.wise.E02.com. (
                     2022102601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@         IN      NS      eden.wise.E02.com.
@         IN      A       192.193.3.3
www       IN      CNAME   eden.wise.E02.com.
@         IN      AAAA    ::1 
```

lalu restart bind9 dengan `service bind9 restart`

Pada client SSS, lakukan ping ke eden.wise.e02.com dan www.eden.wise.e02.com. Lalu untuk melakukan pengecekan alias, ketikkan `host -t CNAME www.eden.wise.e02.com`.

![03  domain eden wise E02 com](https://user-images.githubusercontent.com/52820619/198819451-f17eda21-80aa-4b42-8bc4-7928ea07d58f.png)

### Nomor 4

untuk membuat reverse domain dari wise.e02.com, tambahkan code berikut pada file `/etc/bind/named.conf.local`
```
zone "2.193.192.in-addr.arpa" {
    type master;
    file "/etc/bind/wise/2.193.192.in-addr.arpa";
};
```

lalu buat file `/etc/bind/wise/2.193.192.in-addr.arpa` dengan isi file
```
echo '$TTL    604800
@       IN      SOA     wise.E02.com. root.wise.E02.com. (
                     2022102601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
2.193.192.in-addr.arpa. IN      NS      wise.E02.com.
2                       IN      PTR     wise.E02.com.
```

lalu restart bind9 dengan `service bind9 restart`

Pada client SSS, lakuakn perintah berikut `host -t PTR 192.193.2.2` agar mengetahui domain dari ip tersebut, yaitu eise.e02.com

![04  reverse domain wise E02 com](https://user-images.githubusercontent.com/52820619/198819452-393a0718-abac-4881-bfab-1dbf2b39a3e3.png)

### Nomor 5

untuk membuat dns slave, pertama lakukan sedikit konfigurasi tambahan pada master dns, yaitu wise dengan menambahkan beberapa line pada zone dari file `/etc/bind/named.conf.local`
```
notify yes;
also-notify {192.193.3.2;};
allow-transfer {192.193.3.2;};
```

lalu, pada slave dns server, setelah diinstall bind9, lakuakn ko0nfigurasi pada `/etc/bind/named.conf.local` dengan isi sebagai berikut
```
zone "wise.E02.com" {
    type slave;
    masters {192.193.2.2;};
    file "/var/lib/bind/wise.E02.com";
};
```
 lalu restart bind9 pada kedua server dengan `service bind9 restart`. UNtuk melakukan pengujian, matikan dns server pada wise dengan perintah `service bind9 stop`. lalu lakukan ping ke wise.e02.com
![05 a  stop master DNS](https://user-images.githubusercontent.com/52820619/198819454-05cf4ede-15dd-4aa7-8c4b-1a63600c8165.png)
![05 b  test ping DNS](https://user-images.githubusercontent.com/52820619/198819455-a271ad0f-9010-4ac6-b5ff-f61ed4b30914.png)

### Nomor 6 dan 7
untuk menambahkan subdomain operation dan strix.operation, pada server wise, tambahkan code berikut pada file `/etc/bind/wise/wise.E02.com`
```
ns1       IN      A       192.193.3.2
ns2	    IN	A	  192.193.3.2
operation IN      NS      ns1
strix.operation	IN	NS	ns2
```

lalu pada server berlint, tambahkan code berikut pada `/etc/bind/named.conf.local`
```
zone "operation.wise.E02.com" {
    type master;
    file "/etc/bind/operation/operation.wise.E02.com";
};

zone "strix.operation.wise.E02.com" {
    type master;
    file "/etc/bind/operation/strix.operation.wise.E02.com";
};
```

masih di server berlint, untuk konfigurasi operation.wise.e02.com, buat file `/etc/bind/operation/operation.wise.e02.com` dengan isi sebagai berikut
```
$TTL    604800
@       IN      SOA     operation.wise.E02.com. root.operation.wise.E02.com. (
                     2022102601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       	IN      NS      operation.wise.E02.com.
@       	IN      A       192.193.3.3
www     	IN      CNAME   operation.wise.E02.com.
```
untuk konfigurasi strix.operation.wise.e02.com, buat juga file `/etc/bind/operation/strix.operation.wise.e02.com` dengan isi sebagai berikut
```
$TTL    604800
@       IN      SOA     strix.operation.wise.E02.com. root.strix.operation.wise.E02.com. (
                     2022102601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       	IN      NS      strix.operation.wise.E02.com.
@       	IN      A       192.193.3.3
www     	IN      CNAME   strix.operation.wise.E02.com.
```

lalu, untuk kedua server, buat ubah isi file `/etc/bind/named.conf.options` menjadi seperti berikut
```
options {
        directory "/var/cache/bind";
        allow-query{any;};
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```

lakukan restart dns service pada kedua server. lalu lakukan ping ke operation.wise.e02.com dan juga strix.operation.wise.e02.com

![06  domain operation wise E02 com](https://user-images.githubusercontent.com/52820619/198819457-96bca30f-15c4-4bc9-8ed1-005a30c72874.png)
![07  domain strix operation wise E02 com](https://user-images.githubusercontent.com/52820619/198819458-1ef21a8b-48fb-4474-ad87-40352f60b712.png)

### Nomor 8
pada server wise, lakukan instalasi pada beberapa package dengan perintah berikut
```
apt-get install unzip -y
apt-get install ca-certificates -y
apt-get install apache2 -y
apt-get install php -y
apt-get install libapache2-mod-php7.0 -y
```
buat file `/etc/apache2/sites-available/wise.E02.com.conf` dengan isi sebagai berikut
```
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/wise.E02.com
	  ServerName wise.E02.com
	  ServerAlias www.wise.E02.com

	  ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```

setelah itu, lakukan enable site dengan eprintah `a2ensite wise.E02.com`, lalu restart apache2 dengan perintah `service apache2 restart`

untuk konten webnya, lakukan download dari link yang telah disediakan lalu simpan di storage dengan perintah `curl -L "https://drive.google.com/uc?export=download&id=1S0XhL9ViYN7TyCj2W66BNEXQD2AAAw2e" > ~/file.zip`

lalu, unzip file yang telah didownload tadi dengan perintah `unzip ~/file.zip -d /var/www`

setelah itu rename nama foldernya dari wise menjadi wise.e02.com dengan perintah `mv /var/www/wise /var/www/wise.E02.com`

pada client, setelah dilakukan instalasi lynx dengan perintah `apt-get install lynx`, buka wise.e02.com menggunakan lynx dengan peirntah `lynx wise.e02.com`

![08  web wise E02 com](https://user-images.githubusercontent.com/52820619/198819460-b2b43599-aa58-4ec9-9c3e-371f49ed1d7e.png)

### Nomor 9
untuk menambahkan alias path /home agar mengakses file index.php, tambahkan line berikut pada file `/etc/apache2/sites-available/wise.E02.com.conf`
```
<VirtualHost *:80>
	...
	Alias "/home" "/var/www/wise.E02.com/index.php"
	...
</VirtualHost>
```
lalu, pada client sss, coba untuk mengakses wise.e02.com/home, maka akan tetap terbuka indexnya seperti pada gambar sebelumnya
![09  wise E02 com home](https://user-images.githubusercontent.com/52820619/198819461-407d7ce3-4913-4a6f-a7e1-47c9f4c647a0.png)

### Nomor 10
pada server eden, lakukan hal yang sama dengan nomor 8, namun dengan domain yang disesuaikan menjadi `eden.wise.E02.com`, dan url untuk kontennya yaitu https://drive.google.com/uc?export=download&id=1q9g6nM85bW5T9f5yoyXtDqonUKKCHOTV

setelah itu, lakukan pengecekan melalui client sss dengan perintah `lynx eden.wise.e02.com`
![10  web eden wise E02 com](https://user-images.githubusercontent.com/52820619/198819463-170503f4-06d3-4a80-b73e-396a25a669eb.png)

### Nomor 11
![11  eden wise E02 com public](https://user-images.githubusercontent.com/52820619/198819464-78680685-dc6c-4b54-be18-53232b5f2b6c.png)

### Nomor 12
![12  custom 404 error](https://user-images.githubusercontent.com/52820619/198819467-6106cdd0-504c-4dbc-854f-343a71b3b1a0.png)

### Nomor 13
![13 a  virtual host js](https://user-images.githubusercontent.com/52820619/198819472-35206f86-c085-4abd-83c4-215db5268988.png)
![13 b  virtual host js](https://user-images.githubusercontent.com/52820619/198819475-20ddb516-6d88-4285-9097-cdba1e2b96b1.png)

### Nomor 14
![14 a  disable port 80](https://user-images.githubusercontent.com/52820619/198819476-fa5827c8-3c9a-4e58-b4b0-bda64df7a17a.png)
![14 b  enable port 15000](https://user-images.githubusercontent.com/52820619/198819477-503f582e-3f13-4d96-9c19-8679d39cec18.png)
![14 c  enable 15500](https://user-images.githubusercontent.com/52820619/198819479-af563426-5da0-4838-b38a-ee033db47dde.png)

### Nomor 15
![15  auth](https://user-images.githubusercontent.com/52820619/198819480-e602c421-2b38-4140-bda4-f954036928e1.png)

### Nomor 16
![16 a  access ip](https://user-images.githubusercontent.com/52820619/198819481-3c1188cb-4e41-4d26-b949-0f38beade5a5.png)
![16 b  access ip](https://user-images.githubusercontent.com/52820619/198819482-fc917734-2eda-4031-bfec-260b9ce60963.png)

### Nomor 17
![17 a  rewrite](https://user-images.githubusercontent.com/52820619/198819483-999bef86-aba0-400c-9d1c-09602ff583aa.png)
![17 b  download or cancel](https://user-images.githubusercontent.com/52820619/198819484-6a60bbf2-376c-43f8-9101-91f800d5ae5c.png)
