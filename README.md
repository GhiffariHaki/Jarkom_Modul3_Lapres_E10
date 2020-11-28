# Jarkom_Modul3_Lapres_E10
- Ardy Wahyu Setiawan 05111840000050
- Mochamad Haikal Ghiffari 05111840000095

### 1. Soalnya panjang jadi dibawah :))
Soal :
```
Membuat Topologi jaringan demi kelancaran TA-nya dengan kriteria sebagai berikut:
Anri sudah pernah mempelajari teknik Jaringan Komputer sehingga Anri dapat membuat topologi
tersebut dengan mudah. Bu Meguri memerintahkan Anri untuk menjadikan SURABAYA sebagai
router, MALANG sebagai DNS Server, TUBAN sebagai DHCP server, serta MOJOKERTO sebagai Proxy
server, dan UML lainnya sebagai client.
Bu Meguri berpesan pada Anri untuk menyusun topologi secara hati-hati dan memperhatikan
gambar topologi yang diberikan Bu Meguri.
```
- Ganti topologi.sh
```
# Switch
uml_switch -unix switch1 > /dev/null < /dev/null &
uml_switch -unix switch2 > /dev/null < /dev/null &
uml_switch -unix switch3 > /dev/null < /dev/null &

# Router
xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,10.151.70.45 eth1=daemon,,,switch1 eth2=daemon,,,switch3 eth3=daemon,,,switch2 mem=256M &

# Server
xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch2 mem=160M &
xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &
xterm -T TUBAN -e linux ubd0=TUBAN,jarkom umid=TUBAN eth0=daemon,,,switch2 mem=128M &

# Klien
xterm -T BANYUWANGI -e linux ubd0=BANYUWANGI,jarkom umid=BANYUWANGI eth0=daemon,,,switch3 mem=64M &
xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch1 mem=64M &
xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch1 mem=64M &
xterm -T MADIUN -e linux ubd0=MADIUN,jarkom umid=MADIUN eth0=daemon,,,switch3 mem=64M &

```
- Jangan lupa ritual terlebih dahulu, jalankan ip tables di surabaya
- nano /etc/sysctl.conf (surabaya)
- uncomment net.ipv4.ip_forward=1
- sysctl -p
- ubah /etc/network/interfaces, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100520271-85571480-31cf-11eb-9d88-630d865688e1.png)
![image](https://user-images.githubusercontent.com/57068224/100520272-88520500-31cf-11eb-9156-a6d55daa0a7c.png)
![image](https://user-images.githubusercontent.com/57068224/100520273-89833200-31cf-11eb-8bab-79b046c83349.png)
![image](https://user-images.githubusercontent.com/57068224/100520275-8be58c00-31cf-11eb-87d7-224fdc1476ec.png)
![image](https://user-images.githubusercontent.com/57068224/100520280-8e47e600-31cf-11eb-896c-c1aa4ed9e30b.png)
![image](https://user-images.githubusercontent.com/57068224/100520281-8f791300-31cf-11eb-9ab1-199971ef54f7.png)
![image](https://user-images.githubusercontent.com/57068224/100520282-9142d680-31cf-11eb-8ea9-ed4338e48727.png)
![image](https://user-images.githubusercontent.com/57068224/100520286-930c9a00-31cf-11eb-8619-f6cde2e74ab7.png)

### 2. Karena TUBAN jauh dari client, maka SURABAYA ditunjuk sebagai perantara (DHCP Relay) antara DHCP Server dan client.
- Install DHCP di Tuban
- Setting Config isc-dhcp-server Tuban, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100520308-bb949400-31cf-11eb-8e87-3747548eaa24.png)

- Install DHCP Relay di surabaya
- Setting Config isc-dhcp-relay Surabaya, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100520355-f72f5e00-31cf-11eb-9507-1cbca56880a5.png)

- Kemudian tambahkan subnet NID_DMZ seperti berikut agar dhcp relay bisa berjalan, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100520375-0e6e4b80-31d0-11eb-99d1-f0c2307238bb.png)

- Kemudian ubah semua client yang awalnya static menjadi menggunakan dhcp, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100520387-11693c00-31d0-11eb-88f2-60b5f009c786.png)

### 3 dan 4. Atur Client mendapatkan range tertentu
Soal :
```
(3) Client pada subnet 1 mendapatkan range IP dari 192.168.0.10 sampai 192.168.0.100 dan
192.168.0.110 sampai 192.168.0.200.
(4) Client pada subnet 3 mendapatkan range IP dari 192.168.1.50 sampai 192.168.1.70.
```
- Ubah Config /etc/dhcp/dhcpd.conf, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100520512-981e1900-31d0-11eb-98ad-112c1c74907f.png)

- Hasil, **Gambar :**
![image](https://user-images.githubusercontent.com/57068224/100520609-2c887b80-31d1-11eb-8846-5326e793a238.png)
![image](https://user-images.githubusercontent.com/57068224/100520611-34482000-31d1-11eb-80ad-b1ae7fe5572a.png)

### 5. Client mendapatkan DNS Malang dan DNS 202.46.129.2 dari DHCP
- nano /etc/dhcp/dhcpd.conf pada Tuban tambahkan rangenya, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100520648-5f327400-31d1-11eb-8f88-23c0a1ec607f.png)

- Hasil, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100520649-6194ce00-31d1-11eb-8e8e-fd1658dd1b93.png)

### 6. Client di subnet 1 mendapatkan peminjaman alamat IP selama 5 menit, sedangkan subnet 3 dapat 10 menit
- nano /etc/dhcp/dhcpd.conf pada Tuban tambahkan, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100520681-9ef95b80-31d1-11eb-9412-09ac5e0020aa.png)

-Sudah di test waktu demo

### 7. Membuat User di Proxy
- Data User
```
● User : userta_yyy
● Password : inipassw0rdta_yyy
```
- Install squid dan apache utils di Mojokerto
```
apt-get install squid
apt-get install apache2-utils
```
- Buat user dengan cara mengetik perintah tersebut, lalu masukan password yaitu : inipassw0rdta_e10
```
htpasswd -c /etc/squid/passwd userta_e10
```
- Buat backup terlebih dahulu
```
mv /etc/squid/squid.conf /etc/squid/squid.conf.bak
```
- Edit konfigurasi seperti berikut nano /etc/squid/squid.conf, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100520786-4a0a1500-31d2-11eb-8132-fed28a452e2c.png)

- Hasil, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100520787-4d050580-31d2-11eb-88fd-55de4e1c928c.png)

### 8 & 9. Membuat batas waktu proxy
- Edit file Config untuk batas waktu (/etc/squid/acl.conf), **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100520825-95bcbe80-31d2-11eb-830c-703abeadcd4c.png)

- Edit Config /etc/squid/squid.conf, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100520826-96edeb80-31d2-11eb-994e-712011f81c3d.png)

- Sudah teruji waktu demo
### 10. Setiap dia mengakses google.com, maka akan di redirect menuju monta.if.its.ac.id
- Edit Config /etc/squid/squid.conf, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100520892-fa781900-31d2-11eb-9160-431bc3d26633.png)

- Sudah teruji waktu demo (bingung dokum hasilnya gimana)

### 11. Bu Meguri meminta Anri untuk mengubah error page default squid
- Edit Config /etc/squid/squid.conf, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100521065-eda7f500-31d3-11eb-87c1-4eb125ca19db.png)

- Buat direktori yang sudah kita masukan di pengaturan squid
```
 mkdir /usr/share/squid/errors/image
 cd /usr/share/squid/errors
```
- Copy Semua file dari template, lalu pindah ke folder image
```
cp -r templates/* image/
cd image/
```
- Lalu jalankan wget -N 10.151.36.202/ERR_ACCESS_DENIED
- Hasil, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100521066-eed92200-31d3-11eb-8e65-6a969acb05cb.png)

### 12. Membuat nama domain janganlupa-ta.e10.pw dengan port 8080.
- Install BIND9 di Malang
- Buka configurasi untuk membuat zone baru
```
nano /etc/bind/named.conf.local
```
- Setelah itu, Tambahkan konfigurasi, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100521116-45466080-31d4-11eb-945b-11aa9f1dfd70.png)

- Mkdir dulu directory praktikum di /etc/bind/
- Setelah itu cp /etc/bind/db.local ke janganlupa-ta.e10.pw yang ada di directory praktikum yang sudah dibuat
- Lalu, buatlah konfigurasi janganlupa-ta.e10.pw di directory tersebut, **Gambar :**

![image](https://user-images.githubusercontent.com/57068224/100521153-8d658300-31d4-11eb-8897-757b52df3da9.png)
