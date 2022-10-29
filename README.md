# Laporan Resmi Praktikum Jaringan Komputer Modul 2

## Anggota Kelompok
- 5025201248 - Fajar Zuhri Hadiyanto
- 5025201047 - Sidrotul Munawaroh

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

### Nomor 6

### Nomor 7

### Nomor 8

### Nomor 9

### Nomor 10

### Nomor 11

### Nomor 12

### Nomor 13

### Nomor 14

### Nomor 15

### Nomor 16

### Nomor 17
