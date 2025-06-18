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

## **b. Jebakan Troll**

```sh

```

### Foto Hasil Output

![image alt]()

---
### Kode lengkap troll.c

```c

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
