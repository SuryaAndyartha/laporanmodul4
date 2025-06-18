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

2. Langkah 2
```sh
sudo mkdir -p /mnt/troll

sudo chmod 755 /mnt
sudo chmod 755 /mnt/troll
```
Lalu membuat direktori `troll` pada _folder_ `/mnt`, sehingga akan terbentuk `/mnt/troll` sebagai tempat _mount FUSE_. 

3. Langkah 3
```sh
mkdir -p praktikum-modul-4-d10/troll.c
nano troll.c
```
Setelahnya, membuat _file_ `troll.c` yang pada awalnya akan diisi oleh kode program _FUSE_ yang terdapat pada Modul 4 Sistem Operasi (https://github.com/arsitektur-jaringan-komputer/Modul-Sisop/blob/master/Modul4/README-ID.md).

4. Langkah 4
```sh
sudo nano /etc/fuse.conf
```

5. Langkah 5
```sh
gcc -Wall `pkg-config fuse --cflags` troll.c -o troll `pkg-config fuse --libs`
```

6. Langkah 6
```sh
sudo ./troll -f -o allow_other /mnt/troll
```

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

```c

```

### Foto Hasil Output

![image alt]()

## **d. Trap**

```c

```

### Foto Hasil Output

![image alt]()
