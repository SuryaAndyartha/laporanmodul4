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

# LawakFS++ - A Cursed Filesystem with Censorship and Strict Access Policies

Teja adalah seorang penggemar sepak bola yang sangat bersemangat. Namun, akhir-akhir ini, tim kesayangannya selalu tampil kurang memuaskan di setiap pertandingan. Kekalahan demi kekalahan membuat Teja muak dan kesal. "Tim lawak!" begitu umpatnya setiap kali timnya gagal meraih kemenangan. Kekecewaan Teja yang mendalam ini menginspirasi sebuah ide: bagaimana jika ada sebuah filesystem yang bisa menyensor hal-hal "lawak" di dunia ini?

Untuk mengatasi hal tersebut, kami membuat filesystem terkutuk bernama **LawakFS++** yang mengimplementasikan kebijakan akses yang ketat, filtering konten dinamis, dan kontrol akses berbasis waktu untuk file tertentu. Filesystem ini dirancang sebagai read-only dan akan menerapkan perilaku khusus untuk akses file, termasuk logging dan manajemen konfigurasi.

- Kamu boleh memilih direktori sumber dan mount point apa pun untuk filesystem kamu.

- Kamu **wajib** mengimplementasikan setidaknya fungsi-fungsi berikut dalam struct `fuse_operations` kamu:

  - `getattr`
  - `readdir`
  - `read`
  - `open`
  - `access`

- Kamu diperbolehkan menyertakan fungsi tambahan seperti `init`, `destroy`, atau `readlink` jika diperlukan untuk implementasi kamu.

- **LawakFS++ harus benar-benar read-only.** Setiap percobaan untuk melakukan operasi tulis dalam FUSE mountpoint harus **gagal** dan mengembalikan error `EROFS` (Read-Only File System).

- System call berikut, dan perintah yang bergantung padanya, harus diblokir secara eksplisit:

  - `write()`
  - `truncate()`
  - `create()`
  - `unlink()`
  - `mkdir()`
  - `rmdir()`
  - `rename()`

> **Catatan:** Ketika pengguna mencoba menggunakan perintah seperti `touch`, `rm`, `mv`, atau perintah lain yang melakukan operasi tulis, mereka harus menerima error "Permission denied" atau "Read-only file system" yang jelas.

### a. Ekstensi File Tersembunyi

Setelah beberapa hari menggunakan filesystem biasa, Teja menyadari bahwa ekstensi file selalu membuat orang-orang bisa mengetahui jenis file dengan mudah. "Ini terlalu mudah ditebak!" pikirnya. Dia ingin membuat sistem yang lebih misterius, di mana orang harus benar-benar membuka file untuk mengetahui isinya.

Semua file yang ditampilkan dalam FUSE mountpoint harus **ekstensinya disembunyikan**.

- **Contoh:** Jika file asli adalah `document.pdf`, perintah `ls` di dalam direktori FUSE hanya menampilkan `document`.
- **Perilaku:** Meskipun ekstensi disembunyikan, mengakses file (misalnya, `cat /mnt/your_mountpoint/document`) harus dipetakan dengan benar ke path dan nama aslinya (misalnya, `source_dir/document.pdf`).

### b. Akses Berbasis Waktu untuk File Secret

Suatu hari, Teja menemukan koleksi foto-foto memalukan dari masa SMA-nya yang tersimpan dalam folder bernama "secret". Dia tidak ingin orang lain bisa mengakses file-file tersebut kapan saja, terutama saat dia sedang tidur atau tidak ada di rumah. "File rahasia hanya boleh dibuka saat jam kerja!" putusnya dengan tegas.

File yang nama dasarnya adalah **`secret`** (misalnya, `secret.txt`, `secret.zip`) hanya dapat diakses **antara pukul 08:00 (8 pagi) dan 18:00 (6 sore) waktu sistem**.

- **Pembatasan:** Di luar rentang waktu yang ditentukan, setiap percobaan untuk membuka, membaca, atau bahkan melakukan list file `secret` harus menghasilkan error `ENOENT` (No such file or directory).
- **Petunjuk:** Kamu perlu mengimplementasikan kontrol akses berbasis waktu ini dalam operasi FUSE `access()` dan/atau `getattr()` kamu.

### c. Filtering Konten Dinamis

Kekesalan Teja terhadap hal-hal "lawak" semakin memuncak ketika dia membaca artikel online yang penuh dengan kata-kata yang membuatnya kesal. Tidak hanya itu, gambar-gambar yang dia lihat juga sering kali tidak sesuai dengan ekspektasinya. "Semua konten yang masuk ke sistem saya harus difilter dulu!" serunya sambil mengepalkan tangan.

Ketika sebuah file dibuka dan dibaca, isinya harus **secara dinamis difilter atau diubah** berdasarkan tipe file yang terdeteksi:

| Tipe File      | Perlakuan                                                                                 |
| :------------- | :---------------------------------------------------------------------------------------- |
| **File Teks**  | Semua kata yang dianggap lawak (case-insensitive) harus diganti dengan kata `"lawak"`.    |
| **File Biner** | Konten biner mentah harus ditampilkan dalam **encoding Base64** alih-alih bentuk aslinya. |

> **Catatan:** Daftar "kata-kata lawak" untuk filtering file teks akan didefinisikan secara eksternal, seperti yang ditentukan dalam persyaratan **e. Konfigurasi**.

### d. Logging Akses

Sebagai seorang yang paranoid, Teja merasa perlu untuk mencatat setiap aktivitas yang terjadi di filesystemnya. "Siapa tahu ada yang mencoba mengakses file-file penting saya tanpa izin," gumamnya sambil menyiapkan sistem logging. Dia ingin setiap gerakan tercatat dengan detail, lengkap dengan waktu dan identitas pelakunya.

Semua operasi akses file yang dilakukan dalam LawakFS++ harus **dicatat** ke file yang terletak di **`/var/log/lawakfs.log`**.

Setiap entri log harus mematuhi format berikut:

```
[YYYY-MM-DD HH:MM:SS] [UID] [ACTION] [PATH]
```

Di mana:

- **`YYYY-MM-DD HH:MM:SS`**: Timestamp operasi.
- **`UID`**: User ID pengguna yang melakukan aksi.
- **`ACTION`**: Jenis operasi FUSE (misalnya, `READ`, `ACCESS`, `GETATTR`, `OPEN`, `READDIR`).
- **`PATH`**: Path ke file atau direktori dalam FUSE mountpoint (misalnya, `/secret`, `/images/photo.jpg`).

> **Persyaratan:** Kamu **hanya diwajibkan** untuk mencatat operasi `read` dan `access` yang berhasil. Logging operasi lain (misalnya, write yang gagal) bersifat opsional.

### e. Konfigurasi

Setelah menggunakan filesystemnya beberapa minggu, Teja menyadari bahwa kebutuhannya berubah-ubah. Kadang dia ingin menambah kata-kata baru ke daftar filter, kadang dia ingin mengubah jam akses file secret, atau bahkan mengubah nama file secret itu sendiri. "Saya tidak mau repot-repot kompilasi ulang setiap kali ingin mengubah pengaturan!" keluhnya. Akhirnya dia memutuskan untuk membuat sistem konfigurasi eksternal yang fleksibel.

Untuk memastikan fleksibilitas, parameter-parameter berikut **tidak boleh di-hardcode** dalam source code `lawak.c` kamu. Sebaliknya, mereka harus dapat dikonfigurasi melalui file konfigurasi eksternal (misalnya, `lawak.conf`):

- **Nama file dasar** dari file 'secret' (misalnya, `secret`).
- **Waktu mulai** untuk mengakses file 'secret'.
- **Waktu berakhir** untuk mengakses file 'secret'.
- **Daftar kata-kata yang dipisahkan koma** yang akan difilter dari file teks.

**Contoh konten `lawak.conf`:**

```
FILTER_WORDS=ducati,ferrari,mu,chelsea,prx,onic,sisop
SECRET_FILE_BASENAME=secret
ACCESS_START=08:00
ACCESS_END=18:00
```

FUSE kamu harus membaca dan mem-parse file konfigurasi ini saat inisialisasi.

### Ringkasan Perilaku yang Diharapkan

Untuk memastikan kejelasan, berikut adalah tabel konsolidasi perilaku yang diharapkan untuk skenario tertentu:

| Skenario                                                              | Perilaku yang Diharapkan                                                                         |
| :-------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------- |
| Mengakses file di luar waktu yang diizinkan (misalnya, file `secret`) | Mengembalikan `ENOENT` (No such file or directory)                                               |
| Membaca file biner                                                    | Konten harus dioutput dalam **encoding Base64**                                                  |
| Membaca file teks                                                     | Kata-kata yang difilter harus diganti dengan `"lawak"`                                           |
| Melakukan list file di direktori mana pun                                  | Semua ekstensi file harus disembunyikan                                                          |
| Mencoba menulis, membuat, atau mengganti nama file/direktori          | Mengembalikan `EROFS` (Read-Only File System)                                                    |
| Logging operasi file                                                  | Entri baru harus ditambahkan ke `/var/log/lawakfs.log` untuk setiap operasi `read` dan `access`. |

### Contoh Perilaku

```bash
$ ls /mnt/lawak/
secret   image   readme

$ cat /mnt/lawak/secret
cat: /mnt/lawak/secret: No such file or directory
# (Output ini diharapkan jika diakses di luar 08:00-18:00)

$ cat /mnt/lawak/image
<string base64 dari konten gambar>

$ cat /mnt/lawak/readme
"Ini adalah filesystem lawak."
# (Kata "sisop" asli diganti dengan "lawak")

$ sudo tail /var/log/lawakfs.log
[2025-06-10 14:01:22] [1000] [READ] /readme
[2025-06-10 14:01:24] [1000] [ACCESS] /secret
```

---

### Penyelesaian

### Kode lengkap lawak.c

```c
#define FUSE_USE_VERSION 28

#include <fuse.h>
#include <stdio.h>
#include <errno.h>
#include <stdlib.h>
#include <string.h>
#include <dirent.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <pwd.h>
#include <ctype.h>


#define MAX_WORDS 100
#define MAX_WORD_LEN 4096

static char *filter_words[MAX_WORDS];
static int filter_word_count = 0;
static char secret_basename[MAX_WORD_LEN] = "";
static int access_start_minutes = -1;
static int access_end_minutes = -1;

static const char *underlying_path = "/home/raden/Documents/my_dir";


static const char b64_table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";



static int parse_time_str(const char *time_str)
{
    int h, m;
    if (sscanf(time_str, "%d:%d", &h, &m) == 2 && h >= 0 && h < 24 && m >= 0 && m < 60)
        return h * 60 + m;
    return -1;
}



static void load_config()
{
    FILE *f = fopen("./lawak.conf", "r");
    if (!f)
        return;

    char line[256];
    while (fgets(line, sizeof(line), f))
    {
        char *eq = strchr(line, '=');
        if (!eq)
            continue;

        *eq = '\0';
        char *key = line;
        char *value = eq + 1;

        value[strcspn(value, "\r\n")] = 0;

        if (strcmp(key, "FILTER_WORDS") == 0)
        {
            char *token = strtok(value, ",");
            while (token && filter_word_count < MAX_WORDS)
            {
                filter_words[filter_word_count++] = strdup(token);
                token = strtok(NULL, ",");
            }
        }
        else if (strcmp(key, "SECRET_FILE_BASENAME") == 0)
        {
            strncpy(secret_basename, value, sizeof(secret_basename) - 1);
        }
        else if (strcmp(key, "ACCESS_START") == 0)
        {
            int minutes = parse_time_str(value);
            if (minutes >= 0) access_start_minutes = minutes;
        }
        else if (strcmp(key, "ACCESS_END") == 0)
        {
            int minutes = parse_time_str(value);
            if (minutes >= 0) access_end_minutes = minutes;
        }
    }

    fclose(f);
}



static void log_action(const char *action, const char *path)
{
    FILE *logf = fopen("/var/log/lawakfs.log", "a");
    if (!logf)
        return;

    time_t now = time(NULL);
    struct tm *lt = localtime(&now);
    char timestamp[32];
    strftime(timestamp, sizeof(timestamp), "%Y-%m-%d %H:%M:%S", lt);

    uid_t uid = fuse_get_context()->uid;

    fprintf(logf, "[%s] [%d] [%s] [%s]\n", timestamp, uid, action, path);
    fclose(logf);
}



static int is_word_boundary(const char *str, int pos, int len)
{
    if (pos == 0)
        return 1;
    if (pos == len)
        return 1;
    return !isalnum((unsigned char)str[pos]);
}



static char *replace_words_in_text(const char *input, size_t *out_len)
{
    const char *replacement = "lawak";
    size_t replacement_len = strlen(replacement);

    size_t input_len = strlen(input);
    size_t bufsize = input_len * replacement_len + 1;
    char *output = malloc(bufsize);
    if (!output)
        return NULL;

    size_t in_pos = 0, out_pos = 0;

    while (in_pos < input_len)
    {
        int matched = 0;
        for (int i = 0; i < filter_word_count; i++)
        {
            size_t wlen = strlen(filter_words[i]);
            if (in_pos + wlen <= input_len &&
                strncasecmp(input + in_pos, filter_words[i], wlen) == 0)
                {
                    if (is_word_boundary(input, in_pos - 1, input_len) && is_word_boundary(input, in_pos + wlen, input_len))
                    {
                        if (out_pos + replacement_len >= bufsize)
                        {
                            bufsize *= 2;
                            char *tmp = realloc(output, bufsize);
                            if (!tmp)
                            {
                                free(output);
                                return NULL;
                            }
                            output = tmp;
                        }
                        memcpy(output + out_pos, replacement, replacement_len);
                        out_pos += replacement_len;
                        in_pos += wlen;
                        matched = 1;
                        break;
                    }
                }
        }
        if (!matched)
        {
            if (out_pos + 1 >= bufsize)
            {
                bufsize *= 2;
                char *tmp = realloc(output, bufsize);
                if (!tmp)
                {
                    free(output);
                    return NULL;
                }
                output = tmp;
            }
            output[out_pos++] = input[in_pos++];
        }
    }
    output[out_pos] = '\0';
    *out_len = out_pos;
    return output;
}



static int is_binary_file(const char *path)
{
    const char *ext = strrchr(path, '.');
    if (!ext)
        return 0;

    ext++;

    const char *binary_exts[] = { "jpg", "jpeg", "png", "gif", "bmp", "pdf", "zip", "exe", NULL };

    for (int i = 0; binary_exts[i] != NULL; i++)
    {
        if (strcasecmp(ext, binary_exts[i]) == 0)
            return 1;
    }
    return 0;
}



static void base64_encode(const unsigned char *src, size_t len, char *out)
{
    int i, j;
    for (i = 0, j = 0; i < len;)
    {
        uint32_t octet_a = i < len ? src[i++] : 0;
        uint32_t octet_b = i < len ? src[i++] : 0;
        uint32_t octet_c = i < len ? src[i++] : 0;

        uint32_t triple = (octet_a << 16) + (octet_b << 8) + octet_c;

        out[j++] = b64_table[(triple >> 18) & 0x3F];
        out[j++] = b64_table[(triple >> 12) & 0x3F];
        out[j++] = (i > len + 1) ? '=' : b64_table[(triple >> 6) & 0x3F];
        out[j++] = (i > len) ? '=' : b64_table[triple & 0x3F];
    }
    out[j] = '\0';
}



static void strip_extension(const char *filename, char *outname, size_t size)
{
    strncpy(outname, filename, size);
    char *dot = strrchr(outname, '.');
    if (dot)
    {
        *dot = '\0';
    }
}



static int resolve_real_path(const char *virtual_path, char real_path[MAX_WORD_LEN])
{
    char temp[MAX_WORD_LEN];
    char *token, *saveptr;
    char current[MAX_WORD_LEN];

    strncpy(temp, virtual_path, sizeof(temp));
    snprintf(current, sizeof(current), "%s", underlying_path);

    token = strtok_r(temp, "/", &saveptr);
    while (token)
    {
        DIR *dp = opendir(current);
        if (!dp)
            return -ENOENT;

        struct dirent *de;
        int found = 0;
        while ((de = readdir(dp)) != NULL)
        {
            char stripped[MAX_WORD_LEN];
            strip_extension(de->d_name, stripped, sizeof(stripped));
            if (strcmp(stripped, token) == 0)
            {
                strncat(current, "/", sizeof(current) - strlen(current) - 1);
                strncat(current, de->d_name, sizeof(current) - strlen(current) - 1);
                found = 1;
                break;
            }
        }
        closedir(dp);
        if (!found)
            return -ENOENT;

        token = strtok_r(NULL, "/", &saveptr);
    }

    strncpy(real_path, current, MAX_WORD_LEN);
    return 0;
}


void to_lowercase(char *str)
{
    while (*str)
    {
        *str = tolower((unsigned char)*str);
        str++;
    }
}


static int is_access_allowed(const char *path)
{

    if (secret_basename[0] == '\0') return 1;
    if (access_start_minutes < 0 || access_end_minutes < 0)
    return 1;

    const char *base = strrchr(path, '/');
    base = base ? base + 1 : path;

    char name_only[MAX_WORD_LEN];
    strip_extension(base, name_only, sizeof(name_only));
    to_lowercase(name_only);

    if (strncmp(name_only, secret_basename, strlen(secret_basename)) != 0)
        return 1;

    time_t now = time(NULL);
    struct tm *lt = localtime(&now);
    int hour = lt->tm_hour;
    int now_minutes = hour * 60 + lt->tm_min;

    return (now_minutes >= access_start_minutes && now_minutes < access_end_minutes);
}



static int fs_getattr(const char *path, struct stat *stbuf)
{
    char real_path[MAX_WORD_LEN];
    if (resolve_real_path(path, real_path) != 0)
        return -ENOENT;

    if (lstat(real_path, stbuf) == -1)
        return -errno;

    return 0;
}



static int fs_readdir(const char *path, void *buf, fuse_fill_dir_t filler, off_t offset, struct fuse_file_info *fi)
{
    (void) offset;
    (void) fi;


    char real_path[MAX_WORD_LEN];
    if (resolve_real_path(path, real_path) != 0)
        return -ENOENT;

    DIR *dp = opendir(real_path);
    if (!dp)
        return -errno;

    filler(buf, ".", NULL, 0);
    filler(buf, "..", NULL, 0);

    struct dirent *de;
    while ((de = readdir(dp)) != NULL)
    {
        if (de->d_name[0] == '.')
            continue;

        struct stat st;
        char full[MAX_WORD_LEN];
        snprintf(full, sizeof(full), "%s/%s", real_path, de->d_name);
        if (stat(full, &st) == -1)
            continue;

        char stripped[MAX_WORD_LEN];
        strip_extension(de->d_name, stripped, sizeof(stripped));

        filler(buf, stripped, NULL, 0);
    }

    closedir(dp);
    return 0;
}



static int fs_open(const char *path, struct fuse_file_info *fi)
{
    char real_path[MAX_WORD_LEN];

    if (resolve_real_path(path, real_path) != 0)
        return -ENOENT;

    int fd = open(real_path, fi->flags);
    if (fd == -1)
        return -errno;

    close(fd);
    return 0;
}



static int fs_read(const char *path, char *buf, size_t size, off_t offset, struct fuse_file_info *fi)
{
    (void) fi;
    char real_path[MAX_WORD_LEN];
    if (resolve_real_path(path, real_path) != 0)
        return -ENOENT;

    if (!is_access_allowed(path))
        return -EACCES;

        if (!is_binary_file(real_path))
        {

            FILE *f = fopen(real_path, "r");
            if (!f)
                return -errno;

            fseek(f, 0, SEEK_END);
            long file_size = ftell(f);
            fseek(f, 0, SEEK_SET);

            char *raw_text = malloc(file_size + 1);
            if (!raw_text)
            {
                fclose(f);
                return -ENOMEM;
            }

            if (fread(raw_text, 1, file_size, f) != (size_t)file_size)
            {
                free(raw_text);
                fclose(f);
                return -EIO;
            }
            raw_text[file_size] = '\0';
            fclose(f);

            size_t replaced_len = 0;
            char *replaced_text = replace_words_in_text(raw_text, &replaced_len);
            free(raw_text);

            if (!replaced_text)
                return -ENOMEM;

            if ((size_t)offset >= replaced_len)
            {
                free(replaced_text);
                return 0;
            }
            if (offset + size > replaced_len)
                size = replaced_len - offset;

            memcpy(buf, replaced_text + offset, size);
            free(replaced_text);
            log_action("READ", path);
            return size;
        }

    FILE *f = fopen(real_path, "rb");
    if (!f)
        return -errno;

    fseek(f, 0, SEEK_END);
    long file_size = ftell(f);
    fseek(f, 0, SEEK_SET);

    unsigned char *raw_data = malloc(file_size);
    if (!raw_data)
    {
        fclose(f);
        return -ENOMEM;
    }

    if (fread(raw_data, 1, file_size, f) != (size_t)file_size)
    {
        free(raw_data);
        fclose(f);
        return -EIO;
    }
    fclose(f);

    size_t encoded_len = 4 * ((file_size + 2) / 3);
    char *encoded = malloc(encoded_len + 1);
    if (!encoded)
    {
        free(raw_data);
        return -ENOMEM;
    }

    base64_encode(raw_data, file_size, encoded);
    free(raw_data);

    if ((size_t)offset >= encoded_len)
    {
        free(encoded);
        return 0;
    }

    if (offset + size > encoded_len)
        size = encoded_len - offset;

    memcpy(buf, encoded + offset, size);
    free(encoded);
    log_action("READ", path);
    return size;
}



static int fs_access(const char *path, int mask)
{
    char real_path[MAX_WORD_LEN];

    if (resolve_real_path(path, real_path) != 0)
        return -ENOENT;

    if (!is_access_allowed(path))
        return -EACCES;

    int res = access(real_path, mask);
    if (res == -1)
        return -errno;

    log_action("ACCESS", path);
    return 0;
}



static int fs_write(const char *path, const char *buf, size_t size, off_t offset, struct fuse_file_info *fi)
{
    (void) path; (void) buf; (void) size; (void) offset; (void) fi;
    return -EROFS;
}
static int fs_truncate(const char *path, off_t size)
{
    (void) path; (void) size;
    return -EROFS;
}
static int fs_create(const char *path, mode_t mode, struct fuse_file_info *fi)
{
    (void) path; (void) mode; (void) fi;
    return -EROFS;
}
static int fs_unlink(const char *path)
{
    (void) path;
    return -EROFS;
}
static int fs_mkdir(const char *path, mode_t mode)
{
    (void) path; (void) mode;
    return -EROFS;
}
static int fs_rmdir(const char *path)
{
    (void) path;
    return -EROFS;
}
static int fs_rename(const char *from, const char *to)
{
    (void) from; (void) to;
    return -EROFS;
}



static struct fuse_operations fs_oper = {
    .getattr = fs_getattr,
    .readdir = fs_readdir,
    .open = fs_open,
    .read = fs_read,
    .access = fs_access,
    .write = fs_write,
    .truncate = fs_truncate,
    .create = fs_create,
    .unlink = fs_unlink,
    .mkdir = fs_mkdir,
    .rmdir = fs_rmdir,
    .rename = fs_rename,
};

int main(int argc, char *argv[]) {
    load_config();
    return fuse_main(argc, argv, &fs_oper, NULL);
}
```

### a. Ekstensi File Tersembunyi

```c
static void strip_extension(const char *filename, char *outname, size_t size)
{
    strncpy(outname, filename, size);
    char *dot = strrchr(outname, '.');
    if (dot)
    {
        *dot = '\0';
    }
}
```
Fungsi `strip_extension()` adalah fungsi yang digunakan untuk menghapus ekstensi _file_ (yaitu bagian setelah titik terakhir). Fungsi ini bisa bekerja dengan baik karena bagian-bagian berikut:

-  ```c
   strncpy(outname, filename, size);
   ```
   Baris ini akan menyalin nama _file_ yang berupa `string` (`filename`) ke variabel luaran bernama `outname` sebesar `size`.

-  ```c
   char *dot = strrchr(outname, '.');
   ```
   Fungsi `strchr()` akan mencari kemunculan terakhir dari karakter `.` dalam `string outname`. Jika ditemukan, maka _pointer_ `dot` akan menunjuk ke posisi karakter `.` terakhir.

-  ```c
   if (dot)
   {
        *dot = '\0';
   }
   ```
   Jika `dot` bernilai `true` (artinya karakter `.` terakhir memang ditemukan), maka karakter yang ditunjuk oleh _pointer_ `dot` tadi diganti dengan _null terminator_ (`\0`). Dengan cara ini, `string outname` hanya akan berisi nama _file_ tanpa ekstensi di belakangnya.

```c
static int resolve_real_path(const char *virtual_path, char real_path[MAX_WORD_LEN])
{
    char temp[MAX_WORD_LEN];
    char *token, *saveptr;
    char current[MAX_WORD_LEN];

    strncpy(temp, virtual_path, sizeof(temp));
    snprintf(current, sizeof(current), "%s", underlying_path);

    token = strtok_r(temp, "/", &saveptr);
    while (token)
    {
        DIR *dp = opendir(current);
        if (!dp)
            return -ENOENT;

        struct dirent *de;
        int found = 0;
        while ((de = readdir(dp)) != NULL)
        {
            char stripped[MAX_WORD_LEN];
            strip_extension(de->d_name, stripped, sizeof(stripped));
            if (strcmp(stripped, token) == 0)
            {
                strncat(current, "/", sizeof(current) - strlen(current) - 1);
                strncat(current, de->d_name, sizeof(current) - strlen(current) - 1);
                found = 1;
                break;
            }
        }
        closedir(dp);
        if (!found)
            return -ENOENT;

        token = strtok_r(NULL, "/", &saveptr);
    }

    strncpy(real_path, current, MAX_WORD_LEN);
    return 0;
}
```
Fungsi `resolve_real_path()` bertujuan untuk menerjemahkan _path_ virtual yang dituliskan _user_ (tanpa ekstensi) menjadi _path_ asli dari _file_ yang ada di direktori (yang masih memiliki ekstensi). Fungsi ini dapat bekerja dengan bagian-bagian berikut:

- ```c
  char temp[MAX_WORD_LEN];
  char *token, *saveptr;
  char current[MAX_WORD_LEN];
  ```
  Di bagian ini, ada tiga variabel yang dideklarasi dan diinisialisasi. `temp` adalah _buffer_ untuk menyalin _path_ virtual supaya bisa dimodifikasi. _Pointer_ `token` dan `saveptr` digunakan untuk memecah _path_ virtual menjadi beberapa komponen (menggunakan `strtok`, akan dijelaskan lebih lanjut). Lalu, `current` akan menyimpan `path` saat ini ketika melakukan pencarian yang bersifat hierarkis. Di sini, `temp` dan `current` memiliki ukuran sebesar `MAX_WORD_LEN` yang merupakan makro untuk mendefinisikan ukuran maksimum `string`.
  
     * ```c
       #define MAX_WORD_LEN 4096
       ```
       Nilai `4096` dipilih karena itu adalah ukuran umum untuk satu halaman memori (`4 KB`).

- ```c
  strncpy(temp, virtual_path, sizeof(temp));
  ```
  Baris ini akan menyalin `virtual_path` ke `temp` sebesar `sizeof(temp)` agar tidak mengubah argumen asli.

- ```c
  snprintf(current, sizeof(current), "%s", underlying_path);
  ```
  Baris ini menginisialisasi variabel `current` dengan `root` dari _underlying directory_ yang asli. Ini akan menjadi titik awal pencarian _file_ sebenarnya.

 - ```c
   token = strtok_r(temp, "/", &saveptr);
   ```
   Di sini, setiap komponen dari _path_ virtual akan diambil dengan pemisah karakter `/`. Dengan cara ini, program bisa mengetahui direktori apa saja yang dilalui dalam proses pencarian _file_.

- ```c
  while (token)
    {
        DIR *dp = opendir(current);
        if (!dp)
            return -ENOENT;
    ...
    }
  ```
  Selama komponen _path_ virtual masih ada, maka program akan membuka direktori saat ini (`current`). Jika tidak bisa dibuka, maka akan mengembalikan _error_ `-ENOENT` (_path_ yang menunjuk suatu direktori atau _file_ tersebut tidak ada).

- ```c
  struct dirent *de;
  int found = 0;
  ```
  Bagian ini akan mendeklarasikan `struct dirent *de` yang merupakan representasi dari suatu entri dalam sebuah direktori. Lalu variabel `found` akan diinisialisasi sebagai `0` sebagai `flag` untuk menandakan apakah _file_/direktori yang cocok dengan nama _virtual_ berhasil ditemukan atau tidak.

- ```c
  while ((de = readdir(dp)) != NULL)
   {
       char stripped[MAX_WORD_LEN];
       strip_extension(de->d_name, stripped, sizeof(stripped));
   ...
   }
  ```
  Perulangan `while` ini akan membaca semua entri dalam direktori yang sudah dibuka sampai `readdir(dp)` mengembalikan `NULL` (sudah tidak ada entri lagi). Dalam `loop` ini, akan dideklarasi variabel `stripped` yang merupakan _buffer_ untuk menyimpan nama _file_ tanpa ekstensi. Fungsi `strip_extension()` di sini juga dipanggil untuk memproses `de->d_name` (nama _file_ asli) yang lalu menyimpan versi tanpa ekstensi ke dalam `stripped`.

- ```c
  if (strcmp(stripped, token) == 0)
       {
           strncat(current, "/", sizeof(current) - strlen(current) - 1);
           strncat(current, de->d_name, sizeof(current) - strlen(current) - 1);
           found = 1;
           break;
       }
  ```

     * ```c
       if (strcmp(stripped, token) == 0)
       {
          ...
       }
       ```
       Program akan melakukan perbandingan antara variabel `stripped` yang merupakan nama _file_ tanpa ekstensi (hasil pemanggilan fungsi `strip_extension`) dan `token` yaitu bagian dari `virtual_path` atau _path_ yang diakses _user_ melalui _FUSE_.

     * ```c
       strncat(current, "/", sizeof(current) - strlen(current) - 1);
       ```
       Baris ini akan menambahkan karakter `/` ke variabel `current` menggunakan fungsi `strncat`. `strcncat` dipakai agar `string` masih bisa digabungkan tetapi tetap ada batas jumlah karakter agar tidak melebihi kapasitas _buffer_. `sizeof(current) - strlen(current) - 1` akan menghitung sisa ukuran yang ada dalam _buffer_ `current` untuk mencegah _overflow_.

     * ```c
       strncat(current, de->d_name, sizeof(current) - strlen(current) - 1);
       ```
       Setelah karakter `/` ditambahkan, program akan menambahkan nama _file_ asli (dengan ekstensi) yang didapatkan dari `de->d_name` menggunakan fungsi `strncat` juga.

     * ```c
       found = 1;
       break;
       ```
       _File_ yang sudah dicari akan ditandai sudah ditemukan dan `loop` akan berhenti dengan pemanggilan `break`.

- ```c
  closedir(dp);
  if (!found)
       return -ENOENT;
  ```
  Setelah membaca direktori, maka direktori tersebut akan ditutup dengan `closedir()`. Jika tidak ditemukan _file_ yang cocok, maka akan mengembalikan pesan _error_ `-ENOENT`.

- ```c
  token = strtok_r(NULL, "/", &saveptr);
  ```
  Baris ini akan mengambil _token_ atau komponen selanjutnya (jika ada).

- ```c
  strncpy(real_path, current, MAX_WORD_LEN);
  ```
  Jika semua komponen berhasil ditemukan, maka program akan menyalin _path_ asli (`current`) ke variabel `string real_path`.

```c
static int fs_readdir(const char *path, void *buf, fuse_fill_dir_t filler, off_t offset, struct fuse_file_info *fi)
{
    (void) offset;
    (void) fi;


    char real_path[MAX_WORD_LEN];
    if (resolve_real_path(path, real_path) != 0)
        return -ENOENT;

    DIR *dp = opendir(real_path);
    if (!dp)
        return -errno;

    filler(buf, ".", NULL, 0);
    filler(buf, "..", NULL, 0);

    struct dirent *de;
    while ((de = readdir(dp)) != NULL)
    {
        if (de->d_name[0] == '.')
            continue;

        struct stat st;
        char full[MAX_WORD_LEN];
        snprintf(full, sizeof(full), "%s/%s", real_path, de->d_name);
        if (stat(full, &st) == -1)
            continue;

        char stripped[MAX_WORD_LEN];
        strip_extension(de->d_name, stripped, sizeof(stripped));

        filler(buf, stripped, NULL, 0);
    }

    closedir(dp);
    return 0;
}

```
Fungsi `fs_readdir()` adalah bentuk implementasi _FUSE_ yang akan menangani tindakan _user_ ketika melakukan pembacaan direktori, seperti perintah `ls`. 

- ```c
  (void) offset;
  (void) fi;
  ```
  Dua baris ini digunakan untuk menghindari `warning` dari program. Variabel `offset` dan `fi` tidak digunakan.
  
- ```c
  char real_path[MAX_WORD_LEN];
  if (resolve_real_path(path, real_path) != 0)
       return -ENOENT;
  ```
  Bagian ini sangat penting untuk memastikan nama _file_ tidak memiliki ekstensi. Fungsi `resolve_real_path` akan mencocokkan _path_ virtual dari _user_ ke _path_ asli atau sumber (yang memiliki ekstensi). Jika _path_ tersebut tidak valid, maka program akan mengembalikan pesan _error_ `-ENOENT`.

- ```c
  DIR *dp = opendir(real_path);
  if (!dp)
       return -errno;
  ```
  Bagian ini akan membuka direktori asli (`real_path`) yang sudah berhasil dipetakan dari _path_ virtual. Jika gagal, maka akan mengembalikan _error_ `-errno`.

- ```c
  filler(buf, ".", NULL, 0);
  filler(buf, "..", NULL, 0);
  ```
  Di sini, program akan menambahkan entri standar `.` (direktori saat ini) dan `..` (direktori yang berada di tingkat atas/_parent directory_) ke hasil. Ini adalah prosedur standar untuk `readdir`.

- ```c
  struct dirent *de;
  while ((de = readdir(dp)) != NULL)
  ```
  Di sini, akan dilakukan iterasi ke seluruh isi direktori yang sedang dibuka. Variabel `de` menyimpan informasi dari masing-masing entri.

- ```c
  if (de->d_name[0] == '.')
    continue;
  ```
  Jika ditemukan _hidden file_ yang diawali titik `.` (contohnya `.basharc`) dalam entri yang ada, maka program akan melewatkannya.

- ```c
  struct stat st;
  char full[MAX_WORD_LEN];
  snprintf(full, sizeof(full), "%s/%s", real_path, de->d_name);
  if (stat(full, &st) == -1)
       continue;
  ```

  * ```c
    struct stat st;
    char full[MAX_WORD_LEN];
    ```
    Dideklarasikan struktur `stat` bernama `st` yang akan menyimpan berbagai informasi dari _file_ atau direktori seperti ukuran, mode, waktu akses, dan sebagainya. Variabel `full` juga akan menjadi _path_ lengkap dari _file_ di direktori. 

  * ```c
    snprintf(full, sizeof(full), "%s/%s", real_path, de->d_name);
    ```
    `snprintf` digunakan untuk menggabungkan `string` (dengan tidak melebihi batas _buffer_). `real_path` adalah direktori sumber yang sedang dibaca, `de->d_name` yaitu nama _file_ saat ini yang sedang diiterasi, dan `full` adalah _path_ lengkap. 
     
- ```c
  char stripped[MAX_WORD_LEN];
  strip_extension(de->d_name, stripped, sizeof(stripped));
  ```
  Bagian ini akan memanggil fungsi `strip_extensuon` sehingga program bisa menghapus ekstensi dari `de->d_name` dan menyimpannya pada variabel `stripped`. Bagian ini juga sangat penting untuk berjalannya penghapusan ekstensi pada nama _file_.  

- ```c
  filler(buf, stripped, NULL, 0);
  ```
  Baris ini akan menambahkan nama _file_ yang sudah dihilangkan ekstensinya ke daftar yang dikembalikan ke _FUSE_. Dengan begini, _user_ hanya akan melihat nama _file_ tanpa ekstensi ketika menggunakan perintah `ls`. 

- ```c
  closedir(dp);
  ```
  Setelah semuanya selesai, direktori akan ditutup dengan `closedir`.

### Lainnya

Selain ketiga fungsi yang sudah dijelaskan, ada bagian penting yang tidak bisa dilupakan demi menyelesaikan _problem a_, yaitu pemanggilan fungsi `resolve_real_path` dalam beberapa fungsi lain, seperti yang ada di bawah ini.

```c
static int fs_getattr(const char *path, struct stat *stbuf)
{
    ...
    if (resolve_real_path(path, real_path) != 0)
        return -ENOENT;
    ...
}
```
Pemanggilan fungsi `resolve_real_path` di fungsi `fs_getattr()`. 

```c
static int fs_readdir(const char *path, void *buf, fuse_fill_dir_t filler, off_t offset, struct fuse_file_info *fi)
{
    ...
    if (resolve_real_path(path, real_path) != 0)
        return -ENOENT;
    ...
}
```
Pemanggilan fungsi `resolve_real_path` di fungsi `fs_readdir()`. 

```c
static int fs_open(const char *path, struct fuse_file_info *fi)
{
    ...
    if (resolve_real_path(path, real_path) != 0)
        return -ENOENT;
    ...
}
```
Pemanggilan fungsi `resolve_real_path` di fungsi `fs_open()`. 

```c
static int fs_read(const char *path, char *buf, size_t size, off_t offset, struct fuse_file_info *fi)
{
    ...
    if (resolve_real_path(path, real_path) != 0)
        return -ENOENT;
    ...
}
```
Pemanggilan fungsi `resolve_real_path` di fungsi `fs_read()`. 

```c
static int fs_access(const char *path, int mask)
{
    ...
    if (resolve_real_path(path, real_path) != 0)
        return -ENOENT;
    ...
}
```
Pemanggilan fungsi `resolve_real_path` di fungsi `fs_access()`. 


### Foto Hasil Output

![image alt]()
![image alt]()
![image alt]()
![image alt]()
![image alt]()
  
### b. Akses Berbasis Waktu untuk File Secret

### c. Filtering Konten Dinamis

### d. Logging Akses

### e. Konfigurasi

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
#include <stdbool.h> //Ditambahkan pada soal d
```
_Library_ pada bahasa C yang memberikan akses kepada tipe data `bool`. Dengan _library_ ini, program bisa melakukan pemeriksaan status apakah _trap_ aktif atau tidak. 

```c
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
```
Ini adalah bagian yang ditambahkan pada fungsi `xmp_getattr()`. Secara garis besar, bagian kode ini memastikan bahwa ketika `DainTontas` memicu pesan _trap_, maka pesan tersebut bisa dikirimkan dengan baik oleh sistem dengan terlebih dahulu mendapatkan informasi attribut _file_-nya.

- ```c
  if(is_trap_active() && strlen(path) >= 4 && strcmp(path + strlen(path) - 4, ".txt") == 0){ ... }
  ```
Ada tiga kondisi yang harus dipenuhi agar proses yang berada di dalam kondisi `if` ini bisa berjalan. 
1. Jika fungsi `is_trap_active()` mengembalikan nilai `true` (fungsi ini akan dijelaskan di bawah)
2. Jika panjang `path` lebih dari atau sama dengan `4`, untuk memastikan bahwa `path` cukup panjang karena program harus memeriksa _file_ `.txt` yang memiliki paling sedikit 4 karakter. 
3. Jika 4 karakter terakhir (`path + strlen(path) - 4`) dari `path` sama dengan `".txt"` yang dibandingkan menggunakan fungsi `strcmp`. 
Ketika ketiga kondisi tersebut dipenuhi, maka akan ada proses lebih lanjut. 

- ```c
  uid_t user_id = fuse_get_context()->uid;
  struct passwd* user_info = getpwuid(user_id);

  if(user_info != NULL && strcmp(user_info->pw_name, "DainTontas") == 0){ ... }
  ```
Potongan kode ini bertujuan untuk memeriksa siapa _user_ yang sedang mengakses _file_. Jika `DainTontas` yang sedang melakukannya, maka akan ada proses lebih lanjut. Jika tidak, maka tidak ada. Bagian ini sudah dijelaskan di atas. 

- ```c
  stbuf->st_mode = S_IFREG | 0444;
  stbuf->st_nlink = 1;
  stbuf->st_size = strlen(ascii_art);
  ```
Ketika `DainTontas` yang mengakses _file_, maka jenis _file_ akan ditetapkan sebagai _file_ biasa (`S_IFREG`) dan izin akses membaca bagi semua _user_ (`0444`). _File_ biasa yang merupakan `.txt` tersebut juga akan memiliki 1 _hard link_ sebagai _default_. Dan juga ukuran dari _file_ akan sebesar panjang dari `string ascii_art`, yaitu pesan _trap_ yang akan diberikan kepada `DainTontas` (`string ascii_art` akan dijelaskan di bawah).



```c
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
```
Ini adalah bagian yang ditambahkan pada fungsi `xmp_read()`. Secara garis besar, potongan kode ini akan memastikan bahwa pembacaan pesan _trap_ yang diaktifkan oleh `DainTontas` sesuai dengan yang diinginkan. 

- ```c
  if(is_trap_active() && strlen(path) >= 4 && strcmp(path + strlen(path) - 4, ".txt") == 0){
       uid_t user_id = fuse_get_context()->uid;
       struct passwd* user_info = getpwuid(user_id);
     
       if(user_info != NULL && strcmp(user_info->pw_name, "DainTontas") == 0){ ... }
  ```
Bagian kode ini pada dasarnya sama dengan yang sudah dijelaskan pada bagian `xmp_getattr()`. Bagian ini akan memeriksa apakah fungsi `is_trap_active` mengembalikan `true`, panjang `path` lebih dari atau sama dengan `4`, dan `path` berakhiran dengan `.txt`. Akan dilakukan pemeriksaan juga siapa _user_ yang sedang mengakses _file_. Jika `DainTontas` yang sedang melakukannya, maka akan ada proses lebih lanjut, jika bukan, maka tidak ada. 

- ```c
  int len = strlen(ascii_art);
  ```
Potongan kode ini akan mendapatkan panjang `string` dari pesan _trap_ yang akan dikirimkan ke `DainTontas` menggunakan fungsi `strlen`. Nilainya akan disimpan dalam variabel `len` bertipe `integer`.

- ```c
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
  ```
Bagian kode ini juga memiliki logika yang sama seperti yang sudah pernah dijelaskan pada `xmp_read()` di soal C. Pada dasarnya, bagian ini akan memeriksa berapa banyak jumlah karakter atau panjang pesan yang bisa dikirimkan oleh sistem dari serangkaian kondisi `if`. Setelah itu, pesan yang akan dikirimkan tersebut disalin dari `string` pesan ke _buffer_ `buf` menggunakan perulangan `for`. Jumlah karakter yang bisa dikirimkan juga dikembalikan supaya sistem bisa diberi tahu berapa banyak karakter yang berhasil dibaca dan disalin. 

```c
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

```
Ini adalah variabel `char* ascii_art` yang merupakan pesan _trap_. Pesan ini akan dikirimkan ketika `DainTontas` memicu sesuatu di dalam _FUSE_. Pesan trap ini bertuliskan `"Fell for it again reward"` dalam `ASCII ART`. 

```c
bool is_trap_active(){
     return access("/home/ubuntu/praktikum-modul-4-d10/fusedata/.trap", F_OK) == 0;
}
//Bagian yang ditambahkan pada soal d
```
Bagian ini adalah fungsi yang akan memeriksa apakah _trap_ sudah aktif atau belum dengan cara melakukan percobaan akses ke _file_ `.trap` yang ada di direktori `./fusedata`. Argumen `F_OK` bertujuan untuk memeriksa apakah _file_ ada. Jika ada, maka fungsi `access()` akan mengembalikan `0` yang berarti kondisi `return` tersebut `true` dan fungsi `is_trap_active()` juga akan mengembalikan nilai `true`. Pembuatan _file_ pemicu, yaitu `.trap` akan dijelaskan pada bagian fungsi `xmp_write()`.

```
//Bagian yang ditambahkan pada soal d
    .write = xmp_write,
    .truncate = xmp_truncate,   
    .create = xmp_create,
    //Bagian yang ditambahkan pada soal d
```
Pada bagian ini, ditambahkan operasi _FUSE_ pada `struct fuse_operations xmp_oper` sehingga fungsi-fungsi baru yang akan digunakan bisa dipanggil dengan baik. Di sini, ada tiga fungsi yang ditambahkan yaitu `xmp_write()`, `xmp_truncate()`, dan `xmp_create()`. 

```c
static int xmp_write(const char *path, const char *buf, size_t size,
             off_t offset, struct fuse_file_info *fi)
{ ... }
```
Fungsi `xmp_write()` adalah fungsi yang digunakan ketika sistem ingin mencoba menulis ke dalam _file_, misalnya pada saat perintah `echo` digunakan. Tindakan penulisan ke dalam suatu _file_ oleh `DainTontas` ini yang akan menjadi pemicu aktivasi dari _trap_ yang ada pada sistem. Bagian pada fungsi `xmp_write()` yang ditambahkan ada di bawah.  

```c
    if(strcmp(path, "/upload.txt") == 0 && strncmp(buf, "upload", 6) == 0){
        FILE *trap_file = fopen("/home/ubuntu/praktikum-modul-4-d10/fusedata/.trap", "w");

        if(trap_file != NULL){
            fprintf(trap_file, "trap activated.\n");
            fclose(trap_file);
        }   
    }
```
Ini adalah bagian yang ditambahkan pada fungsi `xmp_write()`. 

- ```c
  if(strcmp(path, "/upload.txt") == 0 && strncmp(buf, "upload", 6) == 0){ ... }
  ```
Pada bagian ini, dilakukan pemeriksaan apakah _file_ yang sedang diakses merupakan `upload.txt` atau bukan menggunakan fungsi `strcmp`. Serta, dilakukan pengecekan apakah pesan yang ditulis ke _file_ tersebut adalah `string` `"upload"` atau bukan. `strncmp` akan membandingkan satu per satu isi dari buffer `buf` pada sistem dengan `string "upload"` sebanyak `6` karakter. 

- ```c
  FILE *trap_file = fopen("/home/ubuntu/praktikum-modul-4-d10/fusedata/.trap", "w");
  ```
Jika _file_ yang diakses adalah `upload.txt` dan dituliskan pesan `"upload"` ke dalam _file tersebut_, maka program akan membuka _file_ `.trap` yang akan menjadi penanda apakah _trap_ tersebut aktif atau tidak. Dengan fungsi `fopen` dan opsi `w`, program bisa membuat _file_ (jika belum ada), membukanya, dan menuliskan pesan sesuai yang diinginkan.

- ```c
  if(trap_file != NULL){
       fprintf(trap_file, "trap activated.\n");
       fclose(trap_file);
  }   
  ```
Jika _file_ `.trap` berhasil dibuka dan ada (`trap_file != NULL`), maka program akan menuliskan `"trap activated"` ke _file_ `.trap` dan menutup _file_ tersebut. Penulisan pesan aktivasi _trap_ ke _file_ `.trap` ini bersifat opsional. 

```c
static int xmp_truncate(const char *path, off_t size)
{
     ...
}
```
Fungsi `xmp_truncate()` adalah fungsi yang dipanggil ketika ada _file_ yang ukurannya diubah sebagai antisipasi jika adanya penulisan ke dalam _file_ dengan perintah `echo`. Fungsi ini akan melakukan _reset_ panjang _file_ ke `0` atau melakukan _resize_ isi _file_.

```c
static int xmp_create(const char *path, mode_t mode, struct fuse_file_info *fi)
{
     ...
}
```
Fungsi `xmp_create()` adalah fungsi yang dipanggil ketika ada _file_ yang ingin dibuat. Dengan fungsi ini, _file_ `,trap` yang baru dibuat ketika proses _mount FUSE_ bisa dilakukan. Ketika tidak adanya fungsi ini, proses tersebut akan memberikan pesan `input/output error`. 

### Foto Hasil Output

![image alt](https://github.com/SuryaAndyartha/laporanmodul4/blob/main/Screenshot%202025-06-18%20at%2011.57.59.png?raw=true)

_Trap_ bersifat _persist_
![image alt](https://github.com/SuryaAndyartha/laporanmodul4/blob/main/Screenshot%202025-06-18%20at%2012.00.30.png?raw=true)
![image alt](https://github.com/SuryaAndyartha/laporanmodul4/blob/main/Screenshot%202025-06-18%20at%2012.00.21.png?raw=true)
