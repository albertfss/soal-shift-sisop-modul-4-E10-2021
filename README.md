# soal-shift-sisop-modul-4-E10-2021

## **Kelompok E-10**
- Stefanus Albert Kosim (05111940000096)
- Ahmad Luthfi Hanif (05111940000179)
- Albert Filip Silalahi (05111940000116)

# Table of Contents
- [Soal 1](https://github.com/albertfss/soal-shift-sisop-modul-4-E10-2021#soal-1)
- [Soal 2](https://github.com/albertfss/soal-shift-sisop-modul-4-E10-2021#soal-2)
- [Soal 3](https://github.com/albertfss/soal-shift-sisop-modul-4-E10-2021#soal-3)
- [Soal 4](https://github.com/albertfss/soal-shift-sisop-modul-4-E10-2021#soal-4)
- [Source Code](https://github.com/albertfss/soal-shift-sisop-modul-4-E10-2021#potongan-source-code)
- [Screenshot](https://github.com/albertfss/soal-shift-sisop-modul-4-E10-2021#screenshot)
- [Referensi](https://github.com/albertfss/soal-shift-sisop-modul-4-E10-2021#referensi-template)

# **Soal 1**

- Soal ini dikerjakan oleh 05111940000096 - Stefanus Albert Kosim.
- Sin dan Sei membuat filesystem dengan sistem enkripsi mutakhir.
- Sistem enkripsinya menggunakan atbash-cipher dan yang dienkripsi tidak termasuk ekstensi file (referensi : *https://www.dcode.fr/atbash-cipher*) .
- Filesystem berfungsi normal seperti linux pada umumnya dengan mount source (root) adalah direktori `Downloads` (`home/[user]/Downloads`).
- Solusi nomor 1 akan digabung (tidak dibahas per sub-bagian).
- Karena fungsi yang digunakan untuk nomor 1 dan 2 digabung, maka potongan code akan disisipkan dibawah.

## **1A**

Direktori akan ter-encode jika dibuat (`mkdir`) dengan awalan `AtoZ_`.

## **1B**

Direktori kan ter-encode jika direname dengan awalan `AtoZ_`.

## **1C**

Apabila direktori direname dengan menghapus awalan `AtoZ_`, maka direktori akan ter-decode (menjadi direktori normal).

## **1D**

Setiap pembuatan direktori ter-encode baik itu rename maupun mkdir, akan tercatat dalam file log dengan format :
```
/home/[USER]/Downloads/[Nama Direktori] → /home/[USER]/Downloads/AtoZ_[Nama Direktori]
```

## **1E**

Metode enkripsi ini bersifat rekursif terhadap direktori yang ada di dalamnya.

## **Solusi**

- Karena fuse akan terhubung secara default ke root pada filesystem user, maka terlebih dahulu dihubungkan ke /home/user/Downloads dengan mengubah path yang akan dipass di fungsi yang di-assingn ke res (fungsi-fungsi ini ada pada template).

    ```
    static const char *dirpath = "/home/yanzkosim/Downloads";

    void linkDownloads(const char *path, char *fpath) {
        if(strcmp(path,"/") == 0){
            path=dirpath;
            sprintf(fpath,"%s",path);
        }else
            sprintf(fpath, "%s%s",dirpath,path);
    }
    ```

    Fungsi linkDownloads ini digunakan di setiap fungsi fuse yang ada pada template.

- Untuk melakukan encode dan decode digunakan fungsi yang sama karena jika melakukan enkripsi 2x pada *atbash cipher* akan mengembalikan ke string awal.

    Terdapat 2 method untuk melakukan enkripsi ini.

    1. Jika direktori merupakan direktori ter-encode, maka yang perlu encode adalah file atau direktori yang dibuat (`touch` dan `mkdir`).
    2. Jika direktori memiliki file didalamnya dan bukan direktori ter-encode, kemudian direname sehingga menjadi direktori ter-encode. Fungsi enkripsi yang digunakan adalah dengan membaca direktori hingga selesai kemudian melakukan encode pada setiap file dan direktori yang ada didalamnya (*rekursif*).
    
    Sistem encode pada solusi ini adalah dengan merename file dan direktori sehingga file dan direktori tersebut akan ter-encode baik pada filesystem SinSei maupun pada `/home/user/Downloads`.

    Untuk mengetahui direktori menggunakan `AtoZ_` pada awalannya, maka dilakukan `strstr` dengan haystack filepath dan needle `/AtoZ_`. Digunakan `'/'` untuk memastikan bahwa `AtoZ_` tersebut terdapat di awalan direktori.

    Encode ini digunakan saat melakukan `create`, `mkdir` dan `rename`

- Dalam melakukan decode, perlu dicek terlebih dahulu *source* dan *destination*-nya untuk menghindari enkripsi 2x apabila direktori `AtoZ_` direname menjadi `AtoZ`. Decode dilakukan dengan cara yang sama seperti encode. Decode ini hanya digunakan saat melakukan `rename`.

- Logging dilakukan ketika melakukan `rename` dari direktori tidak ter-encode menjadi direktori ter-decode serta ketika membuat direktori (`mkdir`) ter-encode.

    1. Dalam pembuatan folder (`mkdir`), perlu dicek bahwa pada filepath terdapat `/AtoZ_` dan dipastikan `/AtoZ` merupakan direktori yang berada di akhir (dapat diketahui dengan menggabungkan `strstr` dan `strrchr`).
   ```
   char *fldName = strrchr(fpath, '/');
   if(strstr(fpath, "/AtoZ_") != NULL && strstr(fldName, "/AtoZ_") != NULL) 
   ```
    2. Jika merename direktori, maka hanya perlu memeriksa source dan destination-nya.
   ```
   if(strstr(to, "/AtoZ_") != NULL && strstr(from, "/AtoZ_" == NULL)) 
   ```

   Karena logging pada nomor 1 tidak ada level, maka akan langsung dipanggil fungsi `printlog()` yang ada pada potongan source code dibawah.

## **Kendala**

- Terdapat kesulitan saat melakukan enkripsi dimana file menjadi tidak terbaca.
- Terdapat kebingungan dalam melakukan enkripsi yaitu antara melakukan enkripsi ketika melakukan read, atau me-rename file secara langsung (pada solusi ini enkripsi dilakukan dengan rename file).
- Sulit dalam melakukan debug

# **Soal 2**

## **2A**

Direktori akan ter-encode dengan metode `atbash + ROT13` jika dibuat (`mkdir`) dengan awalan `RX_`.

## **2B**

Direktori kan ter-encode dengan metode `atbash + Vigenere` jika direname dengan awalan `RX_`.

## **2C**

Direktori akan ter-decode jika direktori direname dengan menghilangkan `RX_` di awalan nama direktori. Saat direktori ter-decode, maka file yang ada didalamnya (rekursif) akan kembali ke nama aslinya

## **2D**

Setiap pembuatan direktori ter-encode baik itu rename maupun mkdir, akan tercatat dalam file log (dicatat bersamaan dengan methodnya). Karena format tidak ditentukan maka formatnya dibuat sendiri dengan menambah method sebelum penamaan pada **1D**
```
METHOD::/home/[USER]/Downloads/[Nama Direktori] → /home/[USER]/Downloads/AtoZ_[Nama Direktori]
```

## **2E**

Pada metode enkripsi ini, file-file di direktori asli (/home/user/Downloads) akan dipecah per 1024 bytes, dan jika diakses dengan filesystem buatan Sin dan Sei, file tersebut menjadi normal (tidak terpecah). Contoh ada file test.txt dengan ukuran 3kb, maka akan dipecah sebagai berikut

- test.txt.0000
- test.txt.0001
- test.txt.0002

Dan jika diakses melalui filesystem Sin Sei akan menjadi `test.txt`.

## **Solusi**

- Prinsip encode yang digunakan sama seperti nomor **1a**. Karena encode pada nomor **2a** ini dilakukan 2x, yaitu ***atbash*** dan dilanjut dengan ***ROT13***, maka fungsi nomor 1 akan digunakan kembali, dan ditambahi dengan fungsi ***ROT13*** yang baru. 

    Encode ini hanya digunakan pada `create` dan `mkdir` karena yang terpengaruh adalah file baru yang ada pada direktori yang baru dibuat.

- Dalam melakukan decode, yang terlebih dahulu dijalankan merupakan fungsi ***ROT13*** dan dilanjutkan oleh fungsi ***atbash***.

    Decode dilakukan saat melakukan rename.

- Untuk logging digunakan prinsip yang sama dengan nomor 1 juga, namun string yang akan dicatat ditambahi dengan method yang digunakan oleh user pada filesystem.

MKDIR :
```
if(strstr(fpath, "/RX_") != NULL && strstr(fldName, "/RX_") != NULL) {
        char logbuf[3000];
        char *tmp = strstr(fpath, "/RX_");
        char f[1000];
        char t[1000];
        sprintf(f,"%s/%s", dirpath, tmp+3);
        sprintf(t,"%s/%s", dirpath, tmp);
        sprintf(logbuf,"MKDIR::%s %s %s", f, "->", t);
        printlog(logbuf);
    }
```
RENAME : 
```
if(strstr(to, "/RX_") != NULL && strstr(from, "/RX_") == NULL) {
        char logbuf[3000];
        sprintf(logbuf,"RENAME::%s %s %s", f, "->", t);
        printlog(logbuf);
    }
```

## **Kendala**

- Tidak mengetahui cara membedakan direktori yang direname dengan yang dibuat dengan `mkdir` sehingga tidak bisa mengerjakan 2b.
- Tidak paham cara melakukan split di direktori Downloads namun filenya utuh di filesystem SinSei.

# **Soal 3**

## **3A**

Jika dibuat direktori baru dengan awalan `A_is_a_`, maka direktori tersebut merupakan direktori spesial.

## **3B**

Jika ada direktori yang direname menjadi `A_is_a_[dirName]`, maka direktori tersebut akan menjadi direktori spesial

## **3C**

Direktori akan menjadi normal jika direktori dengan awalan `A_is_a_` direname sehingga tidak mengandung awalan `A_is_a_`.

## **3D**

Direktori spesial adalah direktori yang mengembalikan enkripsi/encoding pada
direktori “AtoZ_” maupun “RX_” namun masing-masing aturan mereka tetap
berjalan pada direktori di dalamnya (sifat recursive “AtoZ_” dan “RX_” tetap
berjalan pada subdirektori).

## **3E**

Pada direktori spesial semua nama file (tidak termasuk ekstensi) pada fuse akan
berubah menjadi lowercase **insensitive** dan diberi ekstensi baru berupa nilai
desimal dari binner perbedaan namanya.

Contohnya jika pada direktori asli nama filenya adalah “FiLe_CoNtoH.txt” maka
pada fuse akan menjadi “file_contoh.txt.1321”. 1321 berasal dari biner


## **Kendala**

- Tidak memahami maksud soal di bagian direktori spesial. Sehingga tidak ada sub-bagian yang bisa dikerjakan

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

Dalam melakukan logging, hanya perlu dipanggil fungsi log yang sesuai dengan levelnya. Fungsi log ini nantinya akan mengambil timestamp, dan menerima parameter berupa `path` direktori yang sedang diakses. Fungsi ini juga digunakan di semua fungsi fuse.

Potongan code level **INFO** :
```
char logbuffer[1000];
sprintf(logbuffer,"%s::%s","READLINK",path);
printInfo(logbuffer);
```

Potongan code level **WARNING** :
```
char logbuffer[1000];
sprintf(logbuffer,"%s::%s","UNLINK",path);
printWarning(logbuffer);
```

Potonga-potongan kode tersebut digunakan ke semua fungsi fuse sesuai dengan level yaitu `unlink` dan `rmdir` akan memanggil `printWarning()`, sedangkan sisanya akan memanggil `printInfo()`.

## **Kendala**

- Tidak paham untuk menggunakan fungsi-fungsi yang ada pada fuse sehingga tidak semua logging pada fungsi dapat diuji (sebagai contoh fungsi `utimens`) .

# **Potongan Source Code**

- Encode-Decode Dir :
    ```
    void endecDir(const char *dirPath, int encode, int type) {
        char currDir[3000];
        strcpy(currDir,dirPath);

        DIR *dp;
        struct dirent *de;
        dp = opendir(currDir);
        if (dp == NULL)
            return;

        while ((de = readdir(dp)) != NULL) {
            if (strcmp(de->d_name,".")==0 || strcmp(de->d_name,"..")==0) {
                continue;
            }
            struct stat st;
            memset(&st, 0, sizeof(st));
            st.st_ino = de->d_ino;
            st.st_mode = de->d_type << 12;
            char fullpath[4000];
            sprintf(fullpath,"%s/%s",currDir,de->d_name);
            if (S_ISREG(st.st_mode)) {
                endecString(fullpath, encode, type);
            } else if (S_ISDIR(st.st_mode)) {
                endecDir(fullpath, encode, type);
                endecString(fullpath, encode, type);
            } else {
                continue;
            }
        }

        closedir(dp);
        return;
    }
    ```
- Encode-Decode String :
    ```
    void endecString(char * string, int encode, int type){
        char filepath[500];
        int start = 0;
        char *tmpChrP;
        int tmpStart;

        strcpy(filepath,string);
        if ((tmpChrP = strrchr(filepath+1,'/'))!=NULL) {
            start = tmpChrP-filepath;
            tmpStart = start;
        } else {
            return;
        }

        int fileExtIdx;
        if ((tmpChrP = strrchr(filepath, '.'))!=NULL) {
            fileExtIdx = tmpChrP-filepath;
        } else {
            fileExtIdx = strlen(filepath);
        }
        if(type == 1) {
            while(start != fileExtIdx) {
            // Iterate until file extension
                filepath[start] = endec1(filepath[start]);
                start++;
            }
        }
        else if(type == 2) {
            while(start != fileExtIdx) {
            // Iterate until file extension
                filepath[start] = endec2(filepath[start], encode);
                start++;
            }
            start = tmpStart;
            while(start != fileExtIdx) {
            // Iterate until file extension
                filepath[start] = endec1(filepath[start]);
                start++;
            }
        }
        
        rename(string,filepath);
    }
    ```
- Main Encode-Decode (return char) :
    
    Atbash :
    ```
    char endec1(char x) {
        int max = 26;

        if(x >= 'a' && x <= 'z') {
            x = x - 'a' + 1;
            x = max - x;
            x += 'a';
        }
        else if( x >= 'A' && x <= 'Z') {
            x = x - 'A' + 1;
            x = max - x;
            x += 'A';
        }
        return x;
    }
    ```  
    ROT13 :
    ```
    char endec2(char x, int encode) {
        // control flow memiliki logic yang sama karena yang perlu diencode hanya alphabet saja
        if(x >= 'a' && x <= 'z') {
            x -= 'a';
            x += 13;
            x %= 26;
            x += 'a';
        }
        else if(x >= 'A' && x <= 'Z'){
            x -= 'A';
            x += 13;
            x %= 26;
            x += 'A';
        }
        return x;
    }
    ```

- Encript from `mkdir` and `create` :
    ```
    void customEncryptSolo1(char *path, int type) {
        char filepath[500];
        int start = 0;
        
        strcpy(filepath,path);

        char *edit = strrchr(filepath, '/');

        if(type == 1) {
            while(edit[start] != '.' && start < strlen(edit)) {
            // Iterate until file extension
                edit[start] = endec1(edit[start]);
                start++;
            }
        }
        else if(type == 2) {
            while(edit[start] != '.' && start < strlen(edit)) {
            // Iterate until file extension
                edit[start] = endec1(edit[start]);
                start++;
            }
            start = 0;
            while(edit[start] != '.' && start < strlen(edit)) {
            // Iterate until file extension
                edit[start] = endec2(edit[start], 1);
                start++;
            }
        }
        rename(path,filepath);
    }
    ```

- Function for logging :
    ```
    void getTime(char * dest){
        char buffer[10000];
        memset(buffer,0,sizeof(buffer));
        time_t t = time(NULL);
        struct tm tm = *localtime(&t);
        strftime(buffer,sizeof(buffer),"%y%m%d-%X",&tm);
        strcpy(dest,buffer);
    }

    void printlog(char * args){
        FILE* log;
        log = fopen(logpath,"a+");
        fprintf(log,"%s\n",args);
        fclose(log);
    }

    void printInfo(char * args){
        char message[10000],timestamp[40];
        getTime(timestamp);
        sprintf(message,"%s::%s:%s","INFO",timestamp,args);
        printlog(message);
    }

    void printWarning(char * args){
        char message[10000],timestamp[40];
        getTime(timestamp);
        sprintf(message,"%s::%s:%s","WARNING",timestamp,args);
        printlog(message);
    }
    ```

# **Screenshot**

- Filesystem terhubung dengan direktori /home/user/Downloads
  
![linked](https://i.ibb.co/CMDNQWs/Screenshot-from-2021-06-12-06-55-31.png)

- File akan ter-encode dengan metode *atbash-cipher* jika awalan dari direktori adalah `AtoZ_` dan ter-decode jika direktori direname dengan menghilangkan `AtoZ_` di awal nama direktori

![encode1](https://i.ibb.co/JK4VtGS/Screenshot-from-2021-06-12-07-02-17.png)

![encode2](https://i.ibb.co/1ZBWkwb/Screenshot-from-2021-06-12-07-05-09.png)

- File akan ter-encode dengan metode *atbash cipher + ROT13* jika direktori dibuat (`mkdir`) dengan awalan `RX_`

![encode3](https://i.ibb.co/ZSDjGxT/Screenshot-from-2021-06-12-16-36-23.png)

- Logging

    - Screenshot 1 dan 2 pada logging menampilkan logging pada nomor 4 dan nomor 1.
    - Screenshot 3 dan 4 pada logging menampilkan logging pada nomor 4 dan nomor 2.

![logging1](https://i.ibb.co/TkywQQR/Screenshot-from-2021-06-12-07-09-29.png)

![logging2](https://i.ibb.co/rygkvYj/Screenshot-from-2021-06-12-07-09-56.png)

![logging3](https://i.ibb.co/FzXM1wB/Screenshot-from-2021-06-12-16-36-54.png)

![logging4](https://i.ibb.co/dBNhnZM/Screenshot-from-2021-06-12-16-37-06.png)

# **Referensi**

- [Template Fuse](https://github.com/asayler/CU-CS3753-PA5/blob/master/fusexmp.c)
