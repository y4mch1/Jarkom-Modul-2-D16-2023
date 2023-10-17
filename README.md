# Jarkom-Modul-2-d16-2023

**Praktikum Jaringan Komputer Modul 2 Tahun 2023**

## Author

| Nama                  | NRP        | 
| --------------------- | ---------- | 
| Dicky Indra Kuncahyo| 5025201250|
| Widian Sasi Disertasiani | 5025211024| 

# Laporan Resmi

## Daftar Isi

- [Laporan Resmi](#laporan-resmi)
  - [Daftar Isi](#daftar-isi)
  - [Topologi](#topologi)
  - [Network Configuration](#network-configuration)
- [Soal 1](#soal-1)
- [Soal 2](#soal-2)
- [Soal 3](#soal-3)
- [Soal 4](#soal-4)
- [Soal 5](#soal-5)
- [Soal 6](#soal-6)
- [Soal 7](#soal-7)
- [Soal 8](#soal-8)
- [Soal 9](#soal-9)
- [Soal 10](#soal-10)
- [Soal 11](#soal-11)
- [Soal 12](#soal-12)
- [Soal 13](#soal-13)
- [Soal 14](#soal-14)
- [Soal 15](#soal-15)
- [Soal 16](#soal-16)
- [Soal 17](#soal-17)
- [Soal 18](#soal-18)
- [Soal 19](#soal-19)
- [Soal 20](#soal-20)

## Topologi

![topologi](https://media.discordapp.net/attachments/1123262899859763303/1163801151205023885/image.png?ex=6540e548&is=652e7048&hm=8454a843af2d877d70bd447ee92a91614367a37852a4c3ea3b5b5cfe5eccf6eb&=&width=1440&height=637)

## Network Configuration

- **Router**

```
auto eth0
 iface eth0 inet dhcp

 auto eth1
 iface eth1 inet static
 	address 192.199.1.1
 	netmask 255.255.255.0

 auto eth2
 iface eth2 inet static
 	address 192.199.2.1
 	netmask 255.255.255.0

```

- **ArjunaLoadBalancer**

```
auto eth0
 iface eth0 inet static
 	address 192.199.2.2
 	netmask 255.255.255.0
 	gateway 192.199.2.1

```

- **YudhitiraDNSMaster**

```
auto eth0
 iface eth0 inet static
 	address 192.199.2.4
 	netmask 255.255.255.0
 	gateway 192.199.2.1

```

- **WerkudaraDNSSlave**

```
auto eth0
 iface eth0 inet static
 	address 192.199.2.3
 	netmask 255.255.255.0
 	gateway 192.199.2.1

```

- **NakulaClient**

```
auto eth0
 iface eth0 inet static
 	address 192.199.1.2
 	netmask 255.255.255.0
 	gateway 192.199.1.1

```

- **SadewaClient**

```
auto eth0
 iface eth0 inet static
 	address 192.199.1.3
 	netmask 255.255.255.0
 	gateway 192.199.1.1

```

- **AbimanyuWebServer**

```
auto eth0
 iface eth0 inet static
 	address 192.199.1.4
 	netmask 255.255.255.0
 	gateway 192.199.1.1

```

- **PrabukusumaWebServer**

```
auto eth0
 iface eth0 inet static
 	address 192.199.1.5
 	netmask 255.255.255.0
 	gateway 192.199.1.1

```

- **WisanggeniWebServer**

```
auto eth0
 iface eth0 inet static
 	address 192.199.1.6
 	netmask 255.255.255.0
 	gateway 192.199.1.1

```

## Soal 2

 Buatlah website utama dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok

### Penyeleseaian

1. Jalankan script berikut pada node **router** untuk memfilter IP apa saja yang dapat melakukan komunikasi melalui **router**. 

```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```
jalankan pada setiap node untuk menghubungkan ke internet
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.199.0.0/16
```

2. Jalankan script berikut pada node **YudhistiraDNSMaster** untuk instalasi bind9.

```
apt-get update
apt-get install bind9 -y
```

3. Masukkan konfigurasi zone berikut pada file `/etc/bind/named.conf.local` untuk mendaftarkan domain arjuna.d16.com pada node **YudhistiraDNSMaster**.

```
zone "arjuna.d16.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.d16.com";
};
```

4. Masukkan konfigurasi berikut ke dalam file `/etc/bind/jarkom/arjuna.d16.com`

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.d16.com. root.arjuna.d16.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      arjuna.d16.com.
@       IN      A       192.199.2.2 ; IP arjuna
www     IN      CNAME   arjuna.d16.com.
@       IN      AAAA    ::1

```

5. Masukkan konfigurasi IP **YudhistiraDNSMaster** ke file `/etc/resolv.conf` pada client **(SadewaClient dan NakulaClient)**.
```
echo nameserver 192.199.2.4 > /etc/resolv.conf

```
### Script
```
apt-get update
apt-get install bind9 -y

echo 'zone "arjuna.d16.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.d16.com";
};'> /etc/bind/named.conf.local

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.d16.com. root.arjuna.d16.com. (
                     2023101001         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      arjuna.d16.com.
@       IN      A       192.199.2.2 ; IP arjuna
www     IN      CNAME   arjuna.d16.com.
@       IN      AAAA    ::1
' > /etc/bind/jarkom/arjuna.d16.com

```
### Tiap client ###

```
echo ‘nameserver 192.199.2.4’ > etc/resolv.conf
```
### Bukti


![soal2-1](https://media.discordapp.net/attachments/1123262899859763303/1163809950989025331/image.png?ex=6540ed7a&is=652e787a&hm=5caded5617953b7b3cc2f38f575dd60a12adda930d948e06d0f306d36bfdce2e&=&width=1059&height=508)

![soal2-2](https://media.discordapp.net/attachments/1123262899859763303/1163810219130900560/image.png?ex=6540edba&is=652e78ba&hm=cda2ca4ac8bc484d75848dede4415fd989b47da067b8dc8aa20c21cdc0863780&=&width=1048&height=489)

## Soal 3

Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.)

### Penyelesaian

1. Dengan cara yang sama seperti [soal-2](#soal-2), masukkan konfigurasi berikut pada file `/etc/bind/named.conf.local` di node **YudhistiraDNSMaster**.

```
zone "arjuna.d16.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.d16.com";
};

zone "abimanyu.d16.com" {
        type master;
        file "/etc/bind/jarkom/abimanyu.d16.com";
};


```

2. Masukkan konfigurasi berikut pada `/etc/bind/jarkom/abimanyu.d16.com` di node **YudhistiraDNSMaster**.

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d16.com. root.abimanyu.d16.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.d16.com.
@       IN      A       192.199.1.4 ; IP Abimanyu
www     IN      CNAME   abimanyu.d16.com.
@       IN      AAAA    ::1


```

3. lakukan restart bind9 dengan command:

```
service bind9 restart

```

### Script

```
echo 'zone "arjuna.d16.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.d16.com";
};

zone "abimanyu.d16.com" {
        type master;
        file "/etc/bind/jarkom/abimanyu.d16.com";
};' > /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/jarkom/abimanyu.d16.com

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d16.com. root.abimanyu.d16.com. (
                        2023101001         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.d16.com.
@       IN      A       192.199.1.4 ; IP Abimanyu
www     IN      CNAME   abimanyu.d16.com.
@       IN      AAAA    ::1

' > /etc/bind/jarkom/abimanyu.d16.com

service bind9 restart
```

# Soal 4

Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

### Penyelesaian 

1. Masukkan konfigurasi berikut pada file `/etc/bind/jarkom/abimanyu.d16.com` sebagai berikut pada **YudhistiraDNSMaster**.

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d16.com. root.abimanyu.d16.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@              IN      NS      abimanyu.d16.com.
@              IN      A       192.199.1.4
www            IN      CNAME   abimanyu.d16.com.
parikesit      IN      A       192.199.1.4
www.parikesit  IN      CNAME   parikesit.abimanyu.d16.com.
@       IN      AAAA    ::1


```
### Script

```
echo '
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d16.com. root.abimanyu.d16.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@              IN      NS      abimanyu.d16.com.
@              IN      A       192.199.1.4
www            IN      CNAME   abimanyu.d16.com.
parikesit      IN      A       192.199.1.4
www.parikesit  IN      CNAME   parikesit.abimanyu.d16.com.
@       IN      AAAA    ::1

' > /etc/bind/jarkom/abimanyu.d16.com
```

# Soal 5

(Buat juga reverse domain untuk domain utama)

### Penyelesaian

4. Membuat reverse DNS untuk **abimanyu.d16.com** yang terhubung ke node AbimanyuWebServer dengan mengkonfigurasikan zone di **YudhistiraDNSMaster** pada file `/etc/bind/named.conf.local` sebagai berikut:

```
zone "arjuna.d16.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.d16.com";
};

zone "abimanyu.d16.com" {
        type master;
        file "/etc/bind/jarkom/abimanyu.d16.com";
};

zone "1.199.192.in-addr.arpa" {
        type master;
        file "/etc/bind/jarkom/1.199.192.in-addr.arpa";
};


```

5. Konfigurasi pada file `/etc/bind/jarkom/1.199.192.in-addr.arpa` sebagai berikut:

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d16.com. root.abimanyu.d16.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
1.199.192.in-addr.arpa.   IN      NS      abimanyu.d16.com.
4                         IN      PTR     abimanyu.d16.com.

```
### Script 
```
no 5. 

echo 'zone "arjuna.d16.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.d16.com";
};

zone "abimanyu.d16.com" {
        type master;
        file "/etc/bind/jarkom/abimanyu.d16.com";
};

zone "1.199.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.199.192.in-addr.arpa";
};' > /etc/bind/named.conf.local


cp /etc/bind/db.local /etc/bind/jarkom/1.199.192.in-addr.arpa

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d16.com. root.abimanyu.d16.com. (
                        2023101001         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
1.199.192.in-addr.arpa.   IN      NS      abimanyu.d16.com.
4                         IN      PTR     abimanyu.d16.com.
' > /etc/bind/jarkom/1.199.192.in-addr.arpa

service bind9 restart
```
### Bukti



![soal5-1](https://media.discordapp.net/attachments/1123262899859763303/1163811179907530902/image.png?ex=6540ee9f&is=652e799f&hm=61875890fc7c75e4c6c145c12e427a8e5774c68b8c71e5cbc869a0ce230e2a8f&=&width=834&height=115)


## Soal 6

Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

### Penyelesaian

1. Tambahkan konfigurasi berikut pada `/etc/bind/named.conf.local` pada **YudhistiraDNSMaster**

```
zone "abimanyu.d16.com" {
        type master;
        notify yes;
        also-notify { 192.199.2.3; }; // IP Werkudara
        allow-transfer { 192.199.2.3; }; // IP Werkudara
        file "/etc/bind/jarkom/abimanyu.d16.com";
};


zone "1.199.192.in-addr.arpa" {
        type master;
        file "/etc/bind/jarkom/1.199.192.in-addr.arpa";
};


```

2. Instalasi bind9 pada **WerkudaraDNSSlave** dan konfigurasi file `/etc/bind/named.conf.local` sebagai berikut:

```
zone "abimanyu.d16.com" {
    type slave;
    masters { 192.199.2.4; }; // IP Yudhistira
    file "/var/lib/bind/abimanyu.d16.com";
};


```

3. lakukan restart bind9

```
service bind9 restart
```
### Script
### Yudhistira ###
```
echo 'zone "abimanyu.d16.com" {
        type master;
        notify yes;
        also-notify { 192.199.2.3; }; // IP Werkudara
        allow-transfer { 192.199.2.3; }; // IP Werkudara
        file "/etc/bind/jarkom/abimanyu.d16.com";
};

zone "1.199.192.in-addr.arpa" {
        type master;
        file "/etc/bind/jarkom/1.199.192.in-addr.arpa";
};' > /etc/bind/named.conf.local'

```
### Wekudara ###

```
apt-get update
apt-get install bind9 -y

echo 'zone "abimanyu.d16.com" {
    type slave;
    masters { 192.199.2.4; }; // IP Yudhistira
    file "/var/lib/bind/abimanyu.d16.com";
};' > /etc/bind/named.conf.local

service bind9 restart
```
### Tiap Client ###

```
echo 'nameserver 192.199.2.3 
nameserver 192.199.2.4' > etc/resolv.conf
```
## Bukti



![soal6-1](https://media.discordapp.net/attachments/1123262899859763303/1163811680279601162/image.png?ex=6540ef17&is=652e7a17&hm=e666448f6e0f0207bf82e847f1843fe51f38b2f25a3f550bef562aaebdfd638f&=&width=1416&height=552)




## Soal 7

Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

### Scripts

1. Konfigurasi file `/etc/bind/jarkom/abimanyu.d16.com` pada **YudhistiraDNSMaster**

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d16.com. root.abimanyu.d16.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@              IN      NS      abimanyu.d16.com.
@              IN      A       192.199.1.4
www            IN      CNAME   abimanyu.d16.com.
parikesit      IN      A       192.199.1.4
www.parikesit  IN      CNAME   parikesit.abimanyu.d16.com.
ns1            IN      A       192.199.2.3      ; IP Werkudara
baratayuda     IN      NS      ns1
@       IN      AAAA    ::1


```

2. Konfigurasi file `/etc/bind/named.conf.options` pada node **YudhistiraDNSMaster**.

```
options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0'\''s placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        //dnssec-validation auto;
        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};


```

3. Lakukan restart bind9.

```
service bind9 restart

```

4. Konfigurasi file `/etc/bind/named.conf.local` pada **WerkudaraDNSSlave**.

```
zone "abimanyu.d16.com" {
    type slave;
    masters { 192.199.2.4; }; // IP Yudhistira
    file "/var/lib/bind/abimanyu.d16.com";
};

zone "baratayuda.abimanyu.d16.com" {
    type master;
    file "/etc/bind/baratayuda/baratayuda.abimanyu.d16.com";
};


```

5. Konfigurasi file `/etc/bind/baratayuda/baratayuda.abimanyu.d16.com`.

```
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.d16.com. root.baratayuda.abimanyu.d16.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@                       IN      NS      baratayuda.abimanyu.d16.com.
@                       IN      A       192.199.1.4 ; IP Abimanyu
www                     IN      CNAME   baratayuda.abimanyu.d16.com.
@                       IN      AAAA    ::1


```

6. Konfigurasi file `/etc/bind/named.conf.options` pada node **WerkudaraDNSSlave**.

```
options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0'\''s placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        //dnssec-validation auto;
        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};

```

7. Lakukan restart bind9 pada **WerkudaraDNSSlave**.

```
service bind9 restart
```

### Script

### Yudhistira
```
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d16.com. root.abimanyu.d16.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@              IN      NS      abimanyu.d16.com.
@              IN      A       192.199.1.4
www            IN      CNAME   abimanyu.d16.com.
parikesit      IN      A       192.199.1.4
www.parikesit  IN      CNAME   parikesit.abimanyu.d16.com.
ns1            IN      A       192.199.2.3      ; IP Werkudara
baratayuda     IN      NS      ns1
@       IN      AAAA    ::1
' > /etc/bind/jarkom/abimanyu.d16.com

echo 'options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0'\''s placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        //dnssec-validation auto;
        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};' > /etc/bind/named.conf.options

service bind9 restart
```
### Wekudara

```
echo 'zone "abimanyu.d16.com" {
    type slave;
    masters { 192.199.2.4; }; // IP Yudhistira
    file "/var/lib/bind/abimanyu.d16.com";
};

zone "baratayuda.abimanyu.d16.com" {
    type master;
    file "/etc/bind/baratayuda/baratayuda.abimanyu.d16.com";
};' > /etc/bind/named.conf.local

echo '; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.d16.com. root.baratayuda.abimanyu.d16.com. (
                              2023101001         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@                       IN      NS      baratayuda.abimanyu.d16.com.
@                       IN      A       192.199.1.4 ; IP Abimanyu
www                     IN      CNAME   baratayuda.abimanyu.d16.com.
@                       IN      AAAA    ::1
' > /etc/bind/baratayuda/baratayuda.abimanyu.d16.com



service bind9 restart
```
### Bukti

![soal7-1](https://media.discordapp.net/attachments/1123262899859763303/1163812486286426232/image.png?ex=6540efd7&is=652e7ad7&hm=7b9d7c6009ba5538347ac7ab05f889f3653b7cea513fc768c2e7ecdffa4e3c51&=&width=1197&height=258)


![soal7-1](https://media.discordapp.net/attachments/1123262899859763303/1163813056275546273/image.png?ex=6540f05f&is=652e7b5f&hm=aedb83598a2dfd24076e2fa347a15863ed2614acf5c5436a6fc42f4b83e376bc&=&width=792&height=85)

## Soal 8

Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

### Scripts

1. Tambahkan konfigurasi berikut pada file `/etc/bind/named.conf.local` pada node **WerkudaraDNSSlave**.

```
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.d16.com. root.baratayuda.abimanyu.d16.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@                       IN      NS      baratayuda.abimanyu.d16.com.
@                       IN      A       192.199.1.4 ; IP Abimanyu
www                     IN      CNAME   baratayuda.abimanyu.d16.com.
rjp                     IN      A       192.199.1.4 ; IP Abimanyu
www.rjp                 IN      CNAME   rjp.baratayuda.abimanyu.d16.com.
@                       IN      AAAA    ::1


```

2. Lakukan restart bind9 pada **WerkudaraDNSSlave**.

```
service bind9 restart
```
### Script

```
echo '; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.d16.com. root.baratayuda.abimanyu.d16.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@                       IN      NS      baratayuda.abimanyu.d16.com.
@                       IN      A       192.199.1.4 ; IP Abimanyu
www                     IN      CNAME   baratayuda.abimanyu.d16.com.
rjp                     IN      A       192.199.1.4 ; IP Abimanyu
www.rjp                 IN      CNAME   rjp.baratayuda.abimanyu.d16.com.
@                       IN      AAAA    ::1

' > /etc/bind/named.conf.local

service bind9 restart
```
### Bukti

![soal8](https://media.discordapp.net/attachments/1123262899859763303/1163813452054274110/image.png?ex=6540f0bd&is=652e7bbd&hm=62d3acde27980dd957cfa6b03174dcf0dc3bd4bbe7899f949b59b583712c6fb7&=&width=1308&height=144)

## Soal 9

Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

### Scripts

1. Jalankan command berikut pada node **PrabukusumaWebService**

```
echo nameserver 192.168.122.1 > /etc/resolv.conf


apt-get update && apt install nginx php php-fpm -y


# Buat folder jarkom
mkdir /var/www/jarkom


# echo ke file /var/www/jarkom/index.php
echo '<?php
echo "Hello World from prabukusuma";
?>' > /var/www/jarkom/index.php


echo '
 server {


        listen 80;


        root /var/www/jarkom;


        index index.php index.html index.htm;
        server_name _;


        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }


        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        }


 location ~ /\.ht {
                        deny all;
        }


        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }
' > /etc/nginx/sites-available/jarkom


ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom


rm /etc/nginx/sites-enabled/default




service nginx restart
service php7.0-fpm stop
service php7.0-fpm start

```

2. Jalankan command berikut pada node **AbimanyuWebServer**.

```
echo nameserver 192.168.122.1 > /etc/resolv.conf


apt-get update && apt install nginx php php-fpm -y


# Buat folder jarkom
mkdir /var/www/jarkom


# echo ke file /var/www/jarkom/index.php
echo '<?php
echo "Hello World from abimanyu";
?>' > /var/www/jarkom/index.php




echo '
 server {


  listen 80;


  root /var/www/jarkom;


  index index.php index.html index.htm;
  server_name _;


  location / {
      try_files $uri $uri/ /index.php?$query_string;
  }


  # pass PHP scripts to FastCGI server
  location ~ \.php$ {
  include snippets/fastcgi-php.conf;
  fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
  }


    location ~ /\.ht {
      deny all;
  }


  error_log /var/log/nginx/jarkom_error.log;
  access_log /var/log/nginx/jarkom_access.log;
 }
' > /etc/nginx/sites-available/jarkom




ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom
rm /etc/nginx/sites-enabled/default


service nginx restart
service php7.0-fpm stop
service php7.0-fpm start

```

3. Jalankan command berikut pada node **WisanggeniWebServer**.

```
echo nameserver 192.168.122.1 > /etc/resolv.conf


apt-get update && apt install nginx php php-fpm -y


# Buat folder jarkom
mkdir /var/www/jarkom


# echo ke file /var/www/jarkom/index.php
echo '<?php
echo "Hello World from wisanggeni";
?>' > /var/www/jarkom/index.php


echo '
 server {


        listen 80;


        root /var/www/jarkom;


        index index.php index.html index.htm;
        server_name _;


        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }


        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        }


 location ~ /\.ht {
                        deny all;
        }


        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }
' > /etc/nginx/sites-available/jarkom


ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom


rm /etc/nginx/sites-enabled/default




service nginx restart
service php7.0-fpm stop
service php7.0-fpm start

```

4. Jalankan command berikut pada node **ArjunaLoadBalancer**.

```
echo nameserver 192.168.122.1 > /etc/resolv.conf


apt-get update


# Install bind9
apt-get install bind9 nginx -y


echo ' # Default menggunakan Round Robin
 upstream myweb  {
  server 192.199.1.5 #IP Prabukusuma
  server 192.199.1.4 #IP Abimanyu
    server 192.199.1.6#IP Wisanggeni
 }


 server {
  listen 80;
  server_name arjuna.d16.com www.arjuna.d16.com;


  location / {
  proxy_pass http://myweb;
  }
 }' > /etc/nginx/sites-available/lb-jarkom


ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled/lb-jarkom




service nginx restart

```

## Soal 10

1. Ubah konfigurasi file `/etc/nginx/sites-available/jarkom` pada node **PrabukusumaWebServer**.

```
echo '
 server {


        listen 8002;


        root /var/www/jarkom;


        index index.php index.html index.htm;
        server_name _;


        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }


        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        }


 location ~ /\.ht {
                        deny all;
        }


        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }
' > /etc/nginx/sites-available/jarkom

```

2. Ubah konfigurasi file `/etc/nginx/sites-available/jarkom` pada node **AbimanyuWebServer**.

```
echo '
 server {


  listen 8001;


  root /var/www/jarkom;


  index index.php index.html index.htm;
  server_name _;


  location / {
      try_files $uri $uri/ /index.php?$query_string;
  }


  # pass PHP scripts to FastCGI server
  location ~ \.php$ {
  include snippets/fastcgi-php.conf;
  fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
  }


    location ~ /\.ht {
      deny all;
  }


  error_log /var/log/nginx/jarkom_error.log;
  access_log /var/log/nginx/jarkom_access.log;
 }
' > /etc/nginx/sites-available/jarkom
```

3. Ubah konfigurasi file `/etc/nginx/sites-available/jarkom` pada node **WisanggeniWebServer**.

```
echo '
 server {


        listen 8003;


        root /var/www/jarkom;


        index index.php index.html index.htm;
        server_name _;


        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }


        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        }


 location ~ /\.ht {
                        deny all;
        }


        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }
' > /etc/nginx/sites-available/jarkom

```

4. Jalankan command berikut pada node **ArjunaLoadBalancer**.

```
echo nameserver 192.168.122.1 > /etc/resolv.conf


apt-get update


# Install bind9
apt-get install bind9 nginx -y


echo ' # Default menggunakan Round Robin
 upstream myweb  {
  server 192.199.1.5:8001; #IP Prabukusuma
  server 192.199.1.4:8002; #IP Abimanyu
  server 192.199.1.6:8003; #IP Wisanggeni
 }


 server {
  listen 80;
  server_name arjuna.d16.com www.arjuna.d16.com;


  location / {
  proxy_pass http://myweb;
  }
 }' > /etc/nginx/sites-available/lb-jarkom


ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled/lb-jarkom




service nginx restart


```

### Bukti

## Soal 11

Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy


## Soal 12

Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.


## Soal 13

 Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy



## Soal 14

 Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).



## Soal 15

Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.


## Soal 16

 Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi www.parikesit.abimanyu.yyy.com/js



## Soal 17

Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.



## Soal 18

 Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.



## Soal 19

 Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)



## Soal 20

Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.
