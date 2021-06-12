# soal-shift-sisop-modul-4-E10-2021

## **Kelompok E-10**
- Stefanus Albert Kosim (05111940000096)
- Ahmad Luthfi Hanif (05111940000179)
- Albert Filip Silalahi (05111940000116)

# **Soal 1**

- Soal ini dikerjakan oleh 05111940000096 - Stefanus Albert Kosim.
- Sin dan Sei membuat filesystem dengan sistem enkripsi mutakhir.
- Sistem enkripsinya menggunakan atbash-cipher dan yang dienkripsi tidak termasuk ekstensi file (referensi : *https://www.dcode.fr/atbash-cipher*) 
- Filesystem berfungsi normal seperti linux pada umumnya dengan mount source (root) adalah direktori `Downloads` (`home/[user]/Downloads`)
- Solusi nomor 1 akan digabung (tidak dibahas per sub-bagian)

## **1A**

Direktori akan ter-encode jika dibuat (`mkdir`) dengan awalan `AtoZ_`.

## **1B**

Direktori kan ter-encode jika direname dengan awalan `AtoZ_`.

## **1C**

Apabila direktori direname dengan menghapus awalan `AtoZ_`, maka direktori akan ter-decode (menjadi direktori normal)

## **1D**

Setiap pembuatan direktori ter-encode baik itu rename maupun mkdir, akan tercatat dalam file log dengan format
```
/home/[USER]/Downloads/[Nama Direktori] → /home/[USER]/Downloads/AtoZ_[Nama Direktori]
```

## **1E**

Metode enkripsi ini bersifat rekursif terhadap direktori yang ada di dalamnya/

## **Solusi**



## **Kendala**

- Terdapat kesulitan saat melakukan enkripsi dimana file menjadi tidak terbaca.
- Terdapat kebingungan dalam melakukan enkripsi yaitu antara melakukan enkripsi ketika melakukan read, atau me-rename file secara langsung (pada solusi ini enkripsi dilakukan dengan rename file).
- Sulit dalam melakukan debug

# **Soal 2**

# **Soal 3**

# **Soal 4**

- Soal ini dikerjakan oleh 05111940000096 - Stefanus Albert Kosim.
- Sin dan Sei membuat file log untuk memudahkan dalam memonitor kegiatan pada filesystem

## **4A**

File log dibuat dengan nama `SinSeiFS.log` sehingga file log akan tersimpan pada `home/user/SinSeiFS.log`.

File log ini akan menyimpan semua system call yang dijalankan pada filesystem (semua fungsi yang ada pada fuse).

## **4B**

Log akan dibagi menjadi 2 level yaitu `WARNING` dan `INFO`

## **4C**

Level `WARNING` akan digunakan untuk system call berupa `unlink` dan `rmdir`.

## **4D**

System call selain yang disebutkan pada **4C** akan dicatat pada level `INFO`.

## **4E**

Format penulisan file log adalah sebagai berikut
```
[Level]::[dd][mm][yyyy]-[HH]:[MM]:[SS]:[CMD]::[DESC :: DESC]
```

- Level : Level logging
- dd    : 2 digit tanggal
- mm    : 2 digit bulan
- yyyy  : 4 digit tahun
- HH    : 2 digit jam (format 24 Jam)
- MM    : 2 digit menit
- SS    : 2 digit detik
- CMD   : System Call yang terpanggil
- DESC  : informasi dan parameter tambahan

Contoh penamaan : 
```
INFO::28052021-10:00:00:CREATE::/test.txt
INFO::28052021-10:01:00:RENAME::/test.txt::/rename.txt
WARNING::28052021-10:02:00:UNLINK::/rename.txt
```

## **Solusi**



## **Kendala**

- Tidak paham untuk menggunakan fungsi-fungsi yang ada pada fuse sehingga tidak semua logging pada fungsi dapat diuji (sebagai contoh fungsi `utimens`) .