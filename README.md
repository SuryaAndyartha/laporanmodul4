[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/V7fOtAk7)
|    NRP     |      Name      |
| :--------: | :------------: |
| 5025241065 | Aji Zaenul Musthofa |
| 5025241093 | Nyoman Surya Hutama Andyartha |
| 5025241104 | 	Raden Kurniawan Agung Fitrianto |

# Praktikum Modul 4 _(Module 4 Lab Work)_

</div>

### Daftar Soal _(Task List)_

- [Task 1 - FUSecure](/task-1/)

- [Task 2 - LawakFS++](/task-2/)

- [Task 3 - Drama Troll](/task-3/)

- [Task 4 - LilHabOS](/task-4/)

### Laporan Resmi Praktikum Modul 4 _(Module 4 Lab Work Report)_

# **Drama Troll**

Dalam sebuah perusahaan penuh drama fandom, seorang karyawan bernama DainTontas telah menyinggung komunitas Gensh _ n, Z _ Z, dan Wut \* ering secara bersamaan. Akibatnya, dua rekan kerjanya, SunnyBolt dan Ryeku, merancang sebuah troll dengan gaya khas: membuat filesystem jebakan menggunakan FUSE.

Mereka membutuhkan bantuanmu, ahli Sistem Operasi, untuk menciptakan filesystem kustom yang bisa mengecoh DainTontas, langsung dari terminal yang dia cintai.

## **a. Pembuatan User**

Buat 3 user di sistem sebagai berikut yang merepresentasikan aktor-aktor yang terlibat dalam _trolling_ kali ini, yaitu:

- DainTontas
- SunnyBolt
- Ryeku

Gunakan `useradd` dan `passwd` untuk membuat akun lokal. User ini akan digunakan untuk mengakses filesystem FUSE yang kamu buat.

## **b. Jebakan Troll**

Untuk menjebak DainTontas, kamu harus menciptakan sebuah filesystem FUSE yang dipasang di `/mnt/troll`. Di dalamnya, hanya akan ada dua file yang tampak sederhana:

- `very_spicy_info.txt` - umpan utama.
- `upload.txt` - tempat DainTontas akan "secara tidak sadar" memicu jebakan.

## **c. Jebakan Troll (Berlanjut)**

Setelah membuat dua file tersebut, SunnyBolt dan Ryeku memintamu untuk membuat jebakan yang telah dirancang oleh mereka. SunnyBolt dan Ryeku yang merupakan kolega lama DainTontas dan tahu akan hal-hal memalukan yang dia pernah lakukan, akan menaruh aib-aib tersebut pada file `very_spicy_info.txt`. Apabila dibuka dengan menggunakan command `cat` oleh user selain DainTontas, akan memberikan output seperti berikut:

```
DainTontas' personal secret!!.txt
```

Untuk mengecoh DainTontas, apabila DainTontas membuka `very_spicy_info.txt`, isinya akan berupa seperti berikut:

```
Very spicy internal developer information: leaked roadmap.docx
```

## **d. Trap**

Suatu hari, DainTontas mengecek PC dia dan menemukan bahwa ada sebuah file baru, `very_spicy_info.txt`. Dia kemudian membukanya dan menemukan sebuah leak roadmap. Karena dia ingin menarik perhatian, dia akan mengupload file tersebut ke sosial media `Xitter` dimana _followernya_ dapat melihat dokumen yang bocor tersebut. Karena dia pernah melakukan hal ini sebelumnya, dia punya script bernama `upload.txt` yang dimana ketika dia melakukan `echo` berisi `upload` pada file tersebut, dia akan otomatis mengupload sebuah file ke akun `Xitter` miliknya.

Namun ini semua adalah rencana SunnyBolt dan Ryeku. Atas bantuanmu, mereka telah mengubah isi dari `upload.txt`. Apabila DainTontas melakukan upload dengan metode `echo` miliknya, maka dia dinyatakan telah "membocorkan" info leak tersebut. Setelah DainTontas membocorkan file tersebut, semua file berekstensi `.txt` akan selalu memunculkan sebuah ASCII Art dari kalimat

```
Fell for it again reward
```

Setelah seharian penuh mencoba untuk membetulkan PC dia, dia baru sadar bahwa file `very_spicy_info.txt` yang dia sebarkan melalui script uploadnya tersebut teryata berisikan aib-aib dia. Dia pun ditegur oleh SunnyBolt dan Ryeku, dan akhirnya dia pun tobat.

## **NOTES**

- `upload.txt` hanyalah berupa file kosong yang dipakai untuk trigger _behavior change_ dari `cat` milik DainTontas. Deskripsi di soal hanyalah sebagai _storytelling_ belaka.
- Trigger dari Trap akan bersifat `persist` bahkan ketika FUSE telah di-unmount ataupun ketika mesin dimatikan/direstart

---

### Penyelesaian

## **a. Pembuatan User**

```sh
sudo useradd -m -s /bin/bash DainTontas
sudo passwd DainTontas

sudo useradd -m -s /bin/bash SunnyBolt
sudo passwd SunnyBolt

sudo useradd -m -s /bin/bash Ryeku
sudo passwd Ryeku
```

### Penjelasan:

```sh
sudo useradd -m -s /bin/bash [namauser]
```
Ini adalah perintah untuk membuat _user_ baru pada _Linux_. Perintah tersebut terdiri dari:
- `sudo` : Memperbolehkan _user_ untuk menjalankan perintah sebagai _superuser_ atau `root`. 
- `useradd` : Sebuah utilitas untuk membuat _user_ secara manual, membutuhkan akses _superuser_ untuk dijalankan.
- `-m` : Sebuah opsi untuk membuat direktori `home` secara otomatis pada _user_ yang telah kita buat (`/home/[namauser]`).
- `-s /bin/bash` : Opsi `-s` berarti `shell` dan `/bin/bash` adalah _path_ ke `bash` untuk memastikan bahwa _user_ yang baru dibuat bisa _login_ ke `bash`.
- `[namauser]` : Nama _user_ sesuai yang diinginkan. Pada soal ini, _user_ yang dibuat adalah `DainTontas`, `SunnyBolt`, dan `Ryeku`. 

```sh
sudo passwd [namauser]
```
Bagian ini merupakan perintah untuk mengatur (membuat atau mengganti) _password_ yang ada pada suatu _user_ di _Linux_.
- `sudo` : Memperbolehkan _user_ untuk menjalankan perintah sebagai _superuser_ atau `root`. 
- `passwd` : Program untuk mengubah _password user_, membutuhkan akses _superuser_ untuk dijalankan.
- `[namauser]` : Nama _user_ sesuai yang diinginkan. Pada soal ini, _user_ yang dibuat adalah `DainTontas`, `SunnyBolt`, dan `Ryeku`. 

Setelah perintah ini dijalankan, maka sistem akan meminta _user_ untuk memasukkan _password_, kali ini _password_ dibebaskan.

### Foto Hasil Output

![image alt](https://github.com/SuryaAndyartha/laporanmodul4/blob/main/Screenshot%202025-06-15%20at%2021.17.19.png?raw=true)
![image alt](https://github.com/SuryaAndyartha/laporanmodul4/blob/main/Screenshot%202025-06-15%20at%2021.19.46.png?raw=true)


## **b. Jebakan Troll**

1. Langkah satu
```sh
mkdir -p praktikum-modul-4-d10/fusedata
cd praktikum-modul-4-d10/fusedata
touch very_spicy_info.txt upload.txt
```
Hal pertama yang dilakukan adalah membuat direktori `fusedata` yang akan menyimpan `very_spicy_info.txt` dan `upload.txt`.
- `mkdir` : Perintah untuk membuat direktori.
- `-p` : Opsi untuk memastikan jika _folder_ yang berada di tingkat yang lebih tinggi dari _folder_ yang akan dibuat (_parent folder_) belum ada, maka _parent folder_ tersebut akan dibuat juga.
- `cd` : Perintah untuk berganti direktori ke tujuan yang diinginkan.
- `touch` : Perintah untuk membuat _file_ baru, jika belum ada.

2. Langkah 2
```sh
sudo mkdir -p /mnt/troll

sudo chmod 755 /mnt
sudo chmod 755 /mnt/troll
```
Lalu membuat direktori `troll` pada _folder_ `/mnt`, sehingga akan terbentuk `/mnt/troll` sebagai tempat _mount FUSE_. Direktori `/mnt` dan `troll` diberikan akses menggunakan `chmod 755` sehingga grup dan _user_ lain bisa _write_ dan _execute_. Hal ini penting karena kita akan mengakses direktori `troll` tersebut dari berbagai _user_.  

3. Langkah 3
```sh
mkdir -p praktikum-modul-4-d10/troll.c
nano troll.c
```
Setelahnya, membuat _file_ `troll.c` dengan perintah `nano`, yang pada awalnya akan diisi oleh kode program _FUSE_ yang terdapat pada Modul 4 Sistem Operasi (https://github.com/arsitektur-jaringan-komputer/Modul-Sisop/blob/master/Modul4/README-ID.md). 

Serta, _path_ direktori yang akan disalin isinya diganti juga sesuai yang diinginkan. Dalam kasus ini, `dirpath` diganti menjadi `/home/ubuntu/praktikum-modul-4-d10/fusedata`.
```sh
static  const  char *dirpath = "/home/ubuntu/praktikum-modul-4-d10/fusedata"; //Path direktori diganti
```

4. Langkah 4
```sh
sudo nano /etc/fuse.conf
```
Langkah berikutnya adalah membuka _file_ konfigurasi _FUSE_ dengan perintah `nano`, di mana bagian `user_allow_other` diaktifkan dengan cara menghilangkan karakter `#` di awal sehingga tidak lagi menjadi _command_. Langkah ini bertujuan untuk memperbolehkan _user_ selain pemilik mengakses _file_ di lokasi _mount_.

5. Langkah 5
```sh
gcc -Wall `pkg-config fuse --cflags` troll.c -o troll `pkg-config fuse --libs`
```
Melakukan kompilasi _file_ `troll.c` sesuai dengan panduan dari Modul 4 Sistem Operasi. _Output_ yang dihasilkan berupa _file_ `troll`.

6. Langkah 6
```sh
sudo ./troll -f -o allow_other /mnt/troll
```
Menjalankan program _FUSE_ bernama `troll`.

- `sudo` : Memperbolehkan _user_ untuk menjalankan perintah sebagai _superuser_ atau `root`. 
- `./troll` : Menjalankan _file_ bernama `troll` yang merupakan hasil kompilasi `troll.c` dari direktori saat ini `(./)`.
- `-f` : Opsi untuk menjalankan program _FUSE_ di _foreground_, yang akan membantu dalam proses _debugging_.
- `-o allow_other` : Opsi _FUSE_ untuk mengizinkan _user_ lain dalam mengakses direktori lokasi _mount_.
- `/mnt/troll` : Lokasi _mount_ dilakukan, yaitu tempat di mana _FUSE_ akan dieksekusi. 

### Foto Hasil Output

- _FUSE_ saat di-_mount_
![image alt](https://github.com/SuryaAndyartha/laporanmodul4/blob/main/Screenshot%202025-06-18%20at%2010.00.01.png?raw=true)
![image alt](https://github.com/SuryaAndyartha/laporanmodul4/blob/main/Screenshot%202025-06-18%20at%2010.00.22.png?raw=true)

- _FUSE_ setelah di-_unmount_
![image alt](https://github.com/SuryaAndyartha/laporanmodul4/blob/main/Screenshot%202025-06-18%20at%2010.00.36.png?raw=true)
![image alt](https://github.com/SuryaAndyartha/laporanmodul4/blob/main/Screenshot%202025-06-18%20at%2010.00.46.png?raw=true)

---
### Kode lengkap troll.c

```c
#define FUSE_USE_VERSION 28
#include <fuse.h>
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <dirent.h>
#include <errno.h>
#include <sys/time.h>
#include <pwd.h> //Ditambahkan pada soal c
#include <stdbool.h> //Ditambahkan pada soal d

//Bagian yang ditambahkan pada soal d
const char* ascii_art =
"  ______   _ _    __             _ _                       _                                        _ \n"
" |  ____| | | |  / _|           (_) |                     (_)                                      | |\n"
" | |__ ___| | | | |_ ___  _ __   _| |_    __ _  __ _  __ _ _ _ __    _ __ _____      ____ _ _ __ __| |\n"
" |  __/ _ \\ | | |  _/ _ \\| '__| | | __|  / _` |/ _` |/ _` | | '_ \\  | '__/ _ \\ \\ /\\ / / _` | '__/ _` |\n"
" | | |  __/ | | | || (_) | |    | | |_  | (_| | (_| | (_| | | | | | | | |  __/\\ V  V / (_| | | | (_| |\n"
" |_|  \\___|_|_| |_| \\___/|_|    |_|\\__|  \\__,_|\\__, |\\__,_|_|_| |_| |_|  \\___| \\_/\\_/ \\__,_|_|  \\__,_|\n"
"                                                __/ |                                                \n"
"                                               |___/                                                 \n";

bool is_trap_active(){
     return access("/home/ubuntu/praktikum-modul-4-d10/fusedata/.trap", F_OK) == 0;
}
//Bagian yang ditambahkan pada soal d

static  const  char *dirpath = "/home/ubuntu/praktikum-modul-4-d10/fusedata"; //Path direktori diganti

static  int  xmp_getattr(const char *path, struct stat *stbuf)
{
    //Bagian yang ditambahkan pada soal d
    if(is_trap_active() && strlen(path) >= 4 && strcmp(path + strlen(path) - 4, ".txt") == 0){
        uid_t user_id = fuse_get_context()->uid;
        struct passwd* user_info = getpwuid(user_id);

        if(user_info != NULL && strcmp(user_info->pw_name, "DainTontas") == 0){
            stbuf->st_mode = S_IFREG | 0444;
            stbuf->st_nlink = 1;
            stbuf->st_size = strlen(ascii_art);
            return 0;
        }
    }
    //Bagian yang ditambahkan pada soal d

    //Bagian yang ditambahkan pada soal c
    if(strcmp(path, "/very_spicy_info.txt") == 0){
        uid_t user_id = fuse_get_context()->uid;
        struct passwd* user_info = getpwuid(user_id);

        if(user_info != NULL && strcmp(user_info->pw_name, "DainTontas") == 0){
            stbuf->st_size = strlen("Very spicy internal developer information: leaked roadmap.docx\n");
        }
        else{
            stbuf->st_size = strlen("DainTontas' personal secret!!.txt\n");
        }

        stbuf->st_mode = S_IFREG | 0444;
        stbuf->st_nlink = 1;

        return 0;
    }
    //Bagian yang ditambahkan pada soal c

    int res;
    char fpath[1000];

    sprintf(fpath,"%s%s",dirpath,path);

    res = lstat(fpath, stbuf);

    if (res == -1) return -errno;

    return 0;
}



static int xmp_readdir(const char *path, void *buf, fuse_fill_dir_t filler, off_t offset, struct fuse_file_info *fi)
{
    char fpath[1000];

    if(strcmp(path,"/") == 0)
    {
        path=dirpath;
        sprintf(fpath,"%s",path);
    } else sprintf(fpath, "%s%s",dirpath,path);

    int res = 0;

    DIR *dp;
    struct dirent *de;
    (void) offset;
    (void) fi;

    dp = opendir(fpath);

    if (dp == NULL) return -errno;

    while ((de = readdir(dp)) != NULL) {
        struct stat st;

        memset(&st, 0, sizeof(st));

        st.st_ino = de->d_ino;
        st.st_mode = de->d_type << 12;
        res = (filler(buf, de->d_name, &st, 0));

        if(res!=0) break;
    }

    closedir(dp);

    return 0;
}

static int xmp_read(const char *path, char *buf, size_t size, off_t offset, struct fuse_file_info *fi)
{
    //Bagian yang ditambahkan pada soal d
    if(is_trap_active() && strlen(path) >= 4 && strcmp(path + strlen(path) - 4, ".txt") == 0){
        uid_t user_id = fuse_get_context()->uid;
        struct passwd* user_info = getpwuid(user_id);

        if(user_info != NULL && strcmp(user_info->pw_name, "DainTontas") == 0){
            int len = strlen(ascii_art);

            if(offset >= len){
                return 0;
            }

            int message_remaining = len - offset;
            int character_count;

            if(size < message_remaining){
                character_count = size;
            }
            else{
                character_count = message_remaining;
            }

            for(int i = 0; i < character_count; i++){
                buf[i] = ascii_art[offset + i];
            }

            return character_count;
        }
    }
    //Bagian yang ditambahkan pada soal d

    //Bagian yang ditambahkan pada soal c
    if(strcmp(path, "/very_spicy_info.txt") == 0){
        uid_t user_id = fuse_get_context()->uid;
        struct passwd* user_info = getpwuid(user_id);

        if(user_info != NULL && strcmp(user_info->pw_name, "DainTontas") == 0){
            char* message_DainTontas = "Very spicy internal developer information: leaked roadmap.docx\n";

            int len = strlen(message_DainTontas);

            if(offset >= len){
                return 0;
            }

            int message_remaining = len - offset;
            int character_count;

            if(size < message_remaining){
                character_count = size;
            }
            else{
                character_count = message_remaining;
            }

            for(int i = 0; i < character_count; i++){
                buf[i] = message_DainTontas[offset + i];
            }

            return character_count;
        }
        else{
            char* message_Other = "DainTontas' personal secret!!.txt\n";

            int len = strlen(message_Other);

            if(offset >= len){
                return 0;
            }

            int message_remaining = len - offset;
            int character_count;

            if(size < message_remaining){
                character_count = size;
            }
            else{
                character_count = message_remaining;
            }

            for(int i = 0; i < character_count; i++){
                buf[i] = message_Other[offset + i];
            }

            return character_count;
        }

        return 0;
    }
    //Bagian yang ditambahkan soal c

    char fpath[1000];
    if(strcmp(path,"/") == 0)
    {
        path=dirpath;

        sprintf(fpath,"%s",path);
    }
    else sprintf(fpath, "%s%s",dirpath,path);

    int res = 0;
    int fd = 0 ;

    (void) fi;

    fd = open(fpath, O_RDONLY);

    if (fd == -1) return -errno;

    res = pread(fd, buf, size, offset);

    if (res == -1) res = -errno;

    close(fd);

    return res;
}

//Bagian yang ditambahkan pada soal d
static int xmp_write(const char *path, const char *buf, size_t size,
             off_t offset, struct fuse_file_info *fi)
{
    if(strcmp(path, "/upload.txt") == 0 && strncmp(buf, "upload", 6) == 0){
        FILE *trap_file = fopen("/home/ubuntu/praktikum-modul-4-d10/fusedata/.trap", "w");

        if(trap_file != NULL){
            fprintf(trap_file, "trap activated.\n");
            fclose(trap_file);
        }   
    }

    int fd;
    int res;
    char fpath[1000];

    sprintf(fpath, "%s%s", dirpath, path);

    (void) fi;
    fd = open(fpath, O_WRONLY);
    if (fd == -1)
        return -errno;

    res = pwrite(fd, buf, size, offset);
    if (res == -1)
        res = -errno;

    close(fd);
    return res;
}

static int xmp_truncate(const char *path, off_t size)
{
    char fpath[1000];
    sprintf(fpath, "%s%s", dirpath, path);

    int res = truncate(fpath, size);
    if (res == -1)
        return -errno;

    return 0;
}

static int xmp_create(const char *path, mode_t mode, struct fuse_file_info *fi)
{
    char fpath[1000];
    sprintf(fpath, "%s%s", dirpath, path);

    int fd = open(fpath, O_CREAT | O_WRONLY, mode);
    if (fd == -1)
        return -errno;

    fi->fh = fd;
    return 0;
}
//Bagian yang ditambahkan pada soal d

static struct fuse_operations xmp_oper = {
    .getattr = xmp_getattr,
    .readdir = xmp_readdir,
    .read = xmp_read,

    //Bagian yang ditambahkan pada soal d
    .write = xmp_write,
    .truncate = xmp_truncate,   
    .create = xmp_create,
    //Bagian yang ditambahkan pada soal d
};



int  main(int  argc, char *argv[])
{
    umask(0);

    return fuse_main(argc, argv, &xmp_oper, NULL);
}
```
---

## **c. Jebakan Troll (Berlanjut)**

### Penjelasan:

```c
#include <pwd.h> //Ditambahkan pada soal c
```
_Library_ pada bahasa C yang memberikan akses kepada fungsi dan tipe data seperti `uid_t`, `getpwuid()`, dan `passwd` yang mengandung `pw_name`. Dengan _library_ ini, program bisa memeriksa siapa _user_ yang sedang mengakses lokasi _mount_ sehingga akan diberikan respons yang sesuai. 

```c
//Bagian yang ditambahkan pada soal c
    if(strcmp(path, "/very_spicy_info.txt") == 0){
        uid_t user_id = fuse_get_context()->uid;
        struct passwd* user_info = getpwuid(user_id);

        if(user_info != NULL && strcmp(user_info->pw_name, "DainTontas") == 0){
            stbuf->st_size = strlen("Very spicy internal developer information: leaked roadmap.docx\n");
        }
        else{
            stbuf->st_size = strlen("DainTontas' personal secret!!.txt\n");
        }

        stbuf->st_mode = S_IFREG | 0444;
        stbuf->st_nlink = 1;

        return 0;
    }
    //Bagian yang ditambahkan pada soal c
```
Ini adalah bagian yang ditambahkan pada fungsi `xmp_getattr()` yang bertujuan untuk mendapatkan informasi atribut dari _file_ yang ada. Atribut yang dimaksud meliputi keberadaan, ukuran, jenis, izin akses, dan lainnya dari sebuah _file_.

- ```c
  if(strcmp(path, "/very_spicy_info.txt") == 0){ ... }
  ``` 
Memeriksa apakah _file_ yang sedang diakses adalah `very_spicy_info.txt` atau bukan menggunakan fungsi `strcmp` yang akan membandingkan `string`. Jika iya (`strcmp` akan mengembalikan `0`), maka ada proses lebih lanjut yang dilakukan.

- ```c
  uid_t user_id = fuse_get_context()->uid;
  ``` 
Bagian ini berfungsi untuk mendapatkan _ID user_ yang sedang mengakses _FUSE_. `fuse_get_context()->uid` akan mengakses data `uid` yang ada pada `struct fuse_context`. Data `uid` tersebut kemudian disimpan dalam variabel `user_id` yang bertipe data `uid_t`. 

- ```c
  struct passwd* user_info = getpwuid(user_id);
  ``` 
Bagian ini bertujuan untuk mendapatkan informasi lengkap dari _user_ yang sudah diperoleh _ID_-nya dengan `getpwuid(user_id)`. `struct passwd` adalah `struct` yang menyimpan beberapa informasi _user_ seperti nama (`pw_name`), _password_ (`pw_password`), dan lainnya. Dengan demikian, data nama _user_ akan disimpan ke dalam `struct passwd` yang sesuai.

- ```c
  if(user_info != NULL && strcmp(user_info->pw_name, "DainTontas") == 0){
       stbuf->st_size = strlen("Very spicy internal developer information: leaked roadmap.docx\n");
  }
  else{
       stbuf->st_size = strlen("DainTontas' personal secret!!.txt\n");
  }
  ```
Jika `getpwuid()` berhasil menemukan informasi _user_ (`user_info` tidak `NULL`) dan nama dari _user_ yang ditemukan adalah `DainTontas` (menggunakan perbandingan `strcmp`), maka ukuran _file_ yang akan dilaporkan ke sistem sebesar panjang `string` dari pesan yang akan disampaikan ke `DainTontas`, yaitu `"Very spicy internal developer information: leaked roadmap.docx"`. Sedangkan, jika _user_ lain yang sedang mengakses _file_-nya, maka ukuran _file_ akan ditetapkan sebesar panjang dari pesan `"DainTontas' personal secret!!.txt"`.

`stbuf->st_size` adalah data pada `stbuf` yang menunjukkan ukuran _file_ yang sedang diakses.

- ```c
  stbuf->st_mode = S_IFREG | 0444;
  ``` 
`stbuf->st_mode` adalah data pada `stbuf` yang menyimpan jenis dan izin akses suatu _file_. Dengan memberikan makro `S_IFREG`, program akan tahu bahwa _file_ yang diakses adalah _file_ biasa (contohnya `.txt`) dan juga izin akses `0444` yang berarti _read-only_ bagi semua _user_. Secara garis besar, program akan tahu bahwa _file_ yang akan diakses adalah _file_ biasa yang bisa dibaca oleh siapa saja.

- ```c
  stbuf->st_nlink = 1;
  ```
`stbuf->st_nlink` adalah data pada `stbuf` yang memberi tahu jumlah _hard link_ pada _file_ yang ingin diakses. Untuk _file_ biasa seperti `.txt`, jumlah _link_ diatur ke angka `1`.

```c
    //Bagian yang ditambahkan pada soal c
    if(strcmp(path, "/very_spicy_info.txt") == 0){
        uid_t user_id = fuse_get_context()->uid;
        struct passwd* user_info = getpwuid(user_id);

        if(user_info != NULL && strcmp(user_info->pw_name, "DainTontas") == 0){
            char* message_DainTontas = "Very spicy internal developer information: leaked roadmap.docx\n";

            int len = strlen(message_DainTontas);

            if(offset >= len){
                return 0;
            }

            int message_remaining = len - offset;
            int character_count;

            if(size < message_remaining){
                character_count = size;
            }
            else{
                character_count = message_remaining;
            }

            for(int i = 0; i < character_count; i++){
                buf[i] = message_DainTontas[offset + i];
            }

            return character_count;
        }
        else{
            char* message_Other = "DainTontas' personal secret!!.txt\n";

            int len = strlen(message_Other);

            if(offset >= len){
                return 0;
            }

            int message_remaining = len - offset;
            int character_count;

            if(size < message_remaining){
                character_count = size;
            }
            else{
                character_count = message_remaining;
            }

            for(int i = 0; i < character_count; i++){
                buf[i] = message_Other[offset + i];
            }

            return character_count;
        }

        return 0;
    }
    //Bagian yang ditambahkan soal c
```
Ini adalah bagian yang ditambahkan pada fungsi `xmp_read()` yang bertujuan untuk membaca isi _file_ dari _disk_ sebenarnya dan memberikannya ke program _FUSE_ yang meminta data, seperti saat melakukan perintah `cat`.

- ```c
  if(strcmp(path, "/very_spicy_info.txt") == 0){
  uid_t user_id = fuse_get_context()->uid;
  struct passwd* user_info = getpwuid(user_id);

  if(user_info != NULL && strcmp(user_info->pw_name, "DainTontas") == 0){ ... }
  ```
Sama seperti yang terdapat pada fungsi `xmp_getattr()`, bagian ini juga melakukan pemeriksaan tentang _file_ apa yang sedang diakses dan siapa _user_ yang sedang mengaksesnya. Jika yang mengakses adalah `DainTontas`, maka akan ada operasi lebih lanjut yang dilakukan.

- ```c
  char* message_DainTontas = "Very spicy internal developer information: leaked roadmap.docx\n";
  ```
Jika _user_ adalah `DainTontas`, maka pesan yang akan dikirimkan, yaitu `"Very spicy internal developer information: leaked roadmap.docx"` disimpan dalam variabel `char` bernama `message_DainTontas`.

- ```c
  int len = strlen(message_DainTontas);
  ```
Potongan kode ini akan mendapatkan panjang `string` dari pesan yang akan dikirimkan ke `DainTontas` menggunakan fungsi `strlen`. Nilainya akan disimpan dalam variabel `len` bertipe `integer`.

- ```c
  if(offset >= len){
      return 0;
  }
  ```
Bagian ini akan memeriksa apakah `offset` sudah melewati panjang _file_ (`len`). Jika iya, maka sudah tidak ada lagi yang perlu dibaca dan program akan mengembalikan nilai `0` untuk berhenti. 

- ```c
  int message_remaining = len - offset;
  int character_count;
  ```
Jika `offset` belum melewati panjang _file_, maka program akan menghitung berapa karakter yang tersisa mulai dari `offset`. Maka dari itu, hasil pengurangan dari total panjang _file_ dengan `offset` atau titik saat ini, akan membentuk variabel `message_remaining`. Variabel `character_count` juga dideklarasikan untuk tahap lebih lanjut.

- ```c
  if(size < message_remaining){
      character_count = size;
  }
  else{
      character_count = message_remaining;
  }
  ```
Potongan kode ini adalah pengecekan terhadap berapa banyak karakter yang boleh dikirim oleh program. Jika `size` (ukuran yang diminta oleh sistem) kurang dari `message_remaining`, maka sistem boleh mengirim keseluruhan `size` tersebut (`character_count = size`). Namun, jika tidak, maka sistem hanya boleh mengirim sisa dari pesan yang ada, yaitu `message_remaining`. 

- ```c
  for(int i = 0; i < character_count; i++){
      buf[i] = message_DainTontas[offset + i];
  }
  ```
Di sini, program akan menyalin karakter satu per satu dari `string` pesan `DainTontas` ke _buffer_ `buf`. `buf[i]` atau `buf` pada posisi ke-`i` akan menyimpan karakter yang ada pada pesan `DainTontas` ke-`i` ditambah `offset` yang ada pada saat itu. 

- ```c
  return character_count;
  ```
Setelah semuanya selesai, akan dikembalikan nilai dari `character_count` sehingga sistem bisa tahu seberapa besar ukuran yang berhasil dibaca dan disalin ke _buffer_. 

- ```c
  else{
       char* message_Other = "DainTontas' personal secret!!.txt\n";
       
       [Rest of the code ...]
  }
  ```
Jika _user_ yang sedang mengakses bukan `DainTontas`, maka pesan yang dikirim akan menjadi `"DainTontas' personal secret!!.txt"`. Untuk proses sisanya, sama saja dengan apa yang sudah dijelaskan pada bagian `DainTontas`.

### Foto Hasil Output

![image alt](https://github.com/SuryaAndyartha/laporanmodul4/blob/main/Screenshot%202025-06-18%20at%2010.03.15.png?raw=true)

## **d. Trap**

```c

```

### Foto Hasil Output

![image alt]()
