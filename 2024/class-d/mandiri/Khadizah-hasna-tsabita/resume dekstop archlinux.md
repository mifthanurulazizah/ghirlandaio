# Arch Linux Hardened — Panduan Instalasi Lengkap

Panduan instalasi Arch Linux dengan konfigurasi hardened (LVM + LUKS + CIS Benchmarks), dipisah berdasarkan dua fase: **Live ISO** dan **Arch-Chroot**.

---

## Fase Live ISO

---

### 1. Koneksi WiFi & Jaringan

Langkah pertama adalah memastikan mesin terhubung ke internet. Jalankan `iwctl` — sebuah utilitas interaktif untuk daemon IWD (iNet wireless daemon) yang bertanggung jawab mengelola koneksi WiFi di Arch. Di dalamnya, gunakan `device list` untuk melihat nama driver WiFi (biasanya `wlan0`), lalu lakukan `scan` dan `get-networks` untuk memindai dan menampilkan SSID yang tertangkap di sekitar. Setelah terhubung, verifikasi koneksi dengan `ping 1.1.1.1` — sebuah utilitas ICMP yang mengirim paket ke server DNS Cloudflare. Jika ada balasan, internet sudah aktif.

```bash
iwctl
device list
station {driver wifi} scan
station {driver wifi} get-networks
station {driver wifi} connect "{nama wifi}"
exit

# Verifikasi koneksi
ping 1.1.1.1
```

---

### 2. Manajemen Disk & Partisi

Gunakan `lsblk` untuk melihat seluruh block device (disk) yang terpasang beserta informasi nama, tipe filesystem, dan ukurannya. Kemudian jalankan `cfdisk` — sebuah program partisi berbasis antarmuka grafis terminal (curses) yang mudah digunakan.

**Skema minimal yang direkomendasikan:**

| Partisi | Ukuran | Tipe           | Fungsi                          |
|---------|--------|----------------|---------------------------------|
| Boot    | 1G     | EFI System     | Menyimpan file bootloader UEFI  |
| Root    | ~49G   | Linux filesystem | Menyimpan sistem OS (untuk LVM) |

```bash
lsblk
lsblk -o name,fstype,size
cfdisk /dev/[partisi]
```

> [!WARNING]
> Jika partisi penting tidak sengaja terhapus, langsung pilih **Quit** — jangan **Write**. Cek ulang kondisi disk dengan `lsblk`.

---

### 3. Setup LVM (Logical Volume)

**LVM (Logical Volume Manager)** adalah fitur kernel Linux untuk alokasi kapasitas harddisk yang sangat fleksibel. Standar CIS Benchmarks menyarankan pemisahan partisi sistem (`/var`, `/tmp`, `/log`) demi keamanan yang lebih granular.

Alur kerjanya adalah tiga langkah berurutan: `pvcreate` menandai disk fisik sebagai **Physical Volume**, `vgcreate` membuat **Volume Group** bernama `proc` yang menampung semua disk fisik tersebut, dan `lvcreate` memotong Volume Group menjadi **Logical Volume** (partisi virtual). Gunakan flag `-L` untuk ukuran pasti, atau `-l 50%FREE` untuk persentase sisa ruang yang tersedia.

```bash
pvcreate /dev/[partisi root]
vgcreate proc /dev/[partisi root]

lvcreate -L 20G proc -n root
lvcreate -L 2G  proc -n vars
lvcreate -L 2G  proc -n vtmp
lvcreate -L 5G  proc -n vlog
lvcreate -L 2G  proc -n vaud
lvcreate -L 10G proc -n home
lvcreate -l 50%FREE proc -n [name]
```

---

### 4. Enkripsi LUKS

**LUKS (Linux Unified Key Setup)** adalah standar enkripsi harddisk pada Linux — melindungi data bahkan saat disk fisik dicuri sekalipun.

- `luksFormat` — menginisialisasi format enkripsi pada partisi target dan meminta Anda menetapkan password master.
- `luksOpen` — membuka partisi terenkripsi dan memetakannya ke nama virtual di `/dev/mapper/...`, siap untuk diformat.

```bash
cryptsetup luksFormat /dev/proc/[name]
cryptsetup luksOpen /dev/proc/[name] [nama device]
mkfs.ext4 /dev/mapper/[nama device]
```

---

### 5. Formatting Filesystem

Formatting adalah proses pembentukan struktur sistem file agar OS dapat membaca dan menulis data. Seluruh partisi LVM diformat dengan `mkfs.ext4` — filesystem ext4 (Extended Filesystem generasi ke-4) yang merupakan standar default Linux modern karena stabilitasnya dan dukungan *journaling*-nya.

Satu-satunya pengecualian adalah partisi Boot yang **wajib** diformat sebagai FAT32 menggunakan `mkfs.vfat -F32`, karena spesifikasi firmware UEFI pada motherboard hanya dapat membaca format FAT32.

```bash
mkfs.ext4 /dev/proc/root
mkfs.vfat -F32 -n BOOT /dev/[partisi boot]
mkfs.ext4 /dev/proc/vars
mkfs.ext4 /dev/proc/vtmp
mkfs.ext4 /dev/proc/vlog
mkfs.ext4 /dev/proc/vaud
mkfs.ext4 /dev/proc/home
mkfs.ext4 /dev/proc/[name]
```

---

### 6. Mounting CIS Layout

Tahap ini menempelkan semua partisi ke dalam struktur direktori OS. Yang membedakan instalasi hardened dari instalasi biasa adalah penggunaan **flag keamanan CIS Benchmarks** pada setiap perintah `mount`:

| Flag      | Fungsi Keamanan                                                                                      |
|-----------|------------------------------------------------------------------------------------------------------|
| `nodev`   | Mencegah interpretasi file karakter/blok ilegal. Melindungi dari pembuatan device ilegal.            |
| `nosuid`  | Mengabaikan bit set-user-identifier (su). Mencegah eskalasi hak akses — user biasa tidak bisa jadi root lewat program. |
| `noexec`  | Mencegah eksekusi file biner langsung dari partisi tersebut. Sangat krusial untuk `/tmp` dan `/home`. |

```bash
mount /dev/proc/root /mnt
mount --mkdir -o uid=0,gid=0,dmask=0077,fmask=0077 /dev/[partisi] /mnt/boot
mount --mkdir -o rw,nodev,nosuid,relatime           /dev/proc/vars /mnt/var
mount --mkdir -o rw,nodev,nosuid,noexec,relatime    /dev/proc/vtmp /mnt/var/tmp
mount --mkdir -o rw,nodev,nosuid,noexec,relatime    /dev/proc/vlog /mnt/var/log
mount --mkdir -o rw,nodev,nosuid,noexec,relatime    /dev/proc/vaud /mnt/var/log/audit
mount --mkdir -o rw,nodev,nosuid,noexec,relatime    /dev/proc/home /mnt/home
```

---

### 7. Instalasi Packages Utama

`pacstrap` adalah skrip khusus Arch untuk menginstal paket langsung ke direktori root baru (`/mnt`). Beberapa paket kunci yang disertakan:

- **`intel-ucode` / `amd-ucode`** — pembaruan mikrokode prosesor untuk menambal celah keamanan hardware seperti Spectre/Meltdown.
- **`linux-lts`** — kernel Linux versi Long Term Support, sangat disarankan untuk sistem hardened demi stabilitas maksimal.
- **`base` & `base-devel`** — kumpulan utilitas minimal sistem GNU/Linux dan alat kompilasi standar.
- **`lvm2`** — utilitas manajemen LVM di userspace.
- **`booster`** — pengganti `mkinitcpio` yang lebih modern dan cepat (ditulis dalam Go).
- **`pam_mount`** — modul PAM untuk otomasi dekripsi LUKS saat login.

```bash
pacstrap /mnt intel-ucode linux-lts linux-lts-headers linux-firmware lvm2 \
  base base-devel neovim openssh superfile podman podman-desktop \
  iptables mpd mpc mpv keepassxc secrets booster networkmanager pam_mount
```

---

### 8. Fstab & Tmpfs

`genfstab` secara otomatis menghasilkan file `/etc/fstab` berdasarkan partisi yang sedang di-mount. File ini adalah **File System Table** — konfigurasi yang dibaca Linux setiap kali sistem booting untuk tahu cara memasang setiap partisi.

```bash
genfstab -U /mnt > /mnt/etc/fstab
```

Setelah itu, tambahkan entri **tmpfs** secara manual. Tmpfs adalah filesystem sementara yang berjalan sepenuhnya di RAM, bukan di disk — sangat cepat, namun isinya hilang saat restart. Diarahkan ke `/tmp` untuk keamanan dan performa ekstra, dilengkapi flag `nosuid`, `nodev`, dan `noexec`.

```bash
echo "/tmpfs /tmp tmpfs defaults,nosuid,nodev,noexec,size=1G 0 0" >> /mnt/etc/fstab
```

---

## Fase Arch-Chroot

Sebelum melanjutkan, masuk ke dalam sistem yang baru saja diinstal menggunakan `arch-chroot`. Perintah ini mengubah direktori root yang aktif sehingga kita berpindah dari lingkungan Live USB ke dalam sistem Arch Linux di harddisk — seolah-olah kita sudah benar-benar berada di dalam OS tersebut.

```bash
arch-chroot /mnt
```

---

### 9. Identitas Sistem & Waktu

Tetapkan **hostname** sebagai nama unik sistem dalam jaringan komputer. Selanjutnya, buat symbolic link dari database zona waktu ke `/etc/localtime` menggunakan `ln -fs` untuk menentukan zona waktu lokal mesin. Terakhir, jalankan `hwclock --systohc` untuk menyinkronisasi jam perangkat keras (BIOS/UEFI motherboard) dengan jam sistem Linux yang diatur dalam UTC.

```bash
echo [nama komputer] > /etc/hostname
ln -fs /usr/share/zoneinfo/Asia/Jakarta /etc/localtime
hwclock --systohc
```

---

### 10. Locale (Bahasa Sistem)

**Locale** adalah sekumpulan variabel lingkungan yang mendefinisikan bahasa, negara, dan format penulisan (tanggal, mata uang, alfabet khusus). Edit `/etc/locale.gen` dan uncomment (hapus tanda `#`) pada baris `en_US.UTF-8` agar sistem mendukung bahasa Inggris internasional dengan karakter Unicode lengkap. Jalankan `locale-gen` untuk mengkompilasi file bahasa yang dipilih.

```bash
nvim /etc/locale.gen
# Cari dengan '/' lalu uncomment kedua baris en_US.UTF-8

locale-gen
locale > /etc/locale.conf

nvim /etc/locale.conf
# Ubah: lang=C  →  LANG=en_US.UTF-8
# Tambah:          LC_ALL=en_US.UTF-8
```

---

### 11. Manajemen User

Buat direktori home, tambahkan akun standar dengan `useradd`, lalu ubah kepemilikan folder home ke user tersebut menggunakan `chown -R`. Berikan hak administrator penuh via `sudoers`, dan jangan lupa set password untuk akun root.

```bash
mkdir /home/user
useradd -d /home/user [username]
passwd [username]
chown -R [username]:[username] /home/user
passwd   # untuk root

echo '[username] ALL=(ALL:ALL) ALL' >> /etc/sudoers.d/none

sudo mount -o rw,nodev,nosuid,relatime /dev/mapper/[nama device] /home/[name]
```

> [!IMPORTANT]
> **Aturan Mutlak:** Password user **harus sama** dengan password LUKS pada partisi tersebut. Ini krusial agar `pam_mount` dapat membongkar enkripsi secara transparan saat login — satu password untuk segalanya.

---

### 12. Konfigurasi pam_mount.conf.xml

**`pam_mount`** adalah modul keamanan Linux yang secara otomatis mengeksekusi perintah mounting disk tepat saat pengguna login dan memasukkan password. Edit file konfigurasinya dan tambahkan tag `<volume>` yang memberitahu modul: ketika `[username]` login, ambil password login-nya, gunakan password tersebut untuk membuka volume LUKS bertipe `crypt` yang beralamat di `/dev/proc/[username]`, lalu mount hasilnya ke `/home/[username]`. Dengan ini, pengguna tidak perlu mengetik password dua kali.

```bash
nvim /etc/security/pam_mount.conf.xml
```

Tambahkan baris berikut di dalam tag `<pam_mount>`:

```xml
<volume user="[username]" fstype="crypt" path="/dev/proc/[username]"
        mountpoint="/home/[username]" options="nodev,nosuid,relatime" />
```

---

### 13. Konfigurasi system-login (PAM)

**PAM (Pluggable Authentication Modules)** adalah sistem sentral Linux yang mengatur seluruh proses otentikasi. Dengan mengedit `/etc/pam.d/system-login`, kita memodifikasi urutan pemeriksaan keamanan yang harus dilewati saat seseorang login di konsol utama.

Baris kunci yang ditambahkan adalah `auth required pam_mount.so` — ini mencegat proses otentikasi dan memberitahu PAM bahwa login juga melibatkan modul pam_mount, sehingga enkripsi LUKS terbuka secara otomatis bersamaan dengan proses login.

```bash
nvim /etc/pam.d/system-login
```

Isi file konfigurasinya:

```
#%PAM-1.0
auth      required   pam_shells.so
auth      requisite  pam_nologin.so
auth      include    system-auth
auth      required   pam_mount.so       ← tambahkan baris ini

account   required   pam_access.so
account   required   pam_nologin.so
account   include    system-auth

password  include    system-auth

session   optional   pam_loginuid.so
session   optional   pam_keyinit.so    force revoke
session   include    system-auth
session   optional   pam_lastlog2.so   silent
session   optional   pam_motd.so
session   optional   pam_mail.so       dir=/var/spool/mail standard quiet
```

---

### 14. Initramfs: Booster

**Initramfs** adalah lingkungan miniatur Linux yang dimuat ke RAM saat booting, sebelum filesystem utama di-mount — diperlukan agar sistem bisa menangani dekripsi LUKS di awal boot. Di sini digunakan **Booster**, program modern pengganti `mkinitcpio` yang ditulis dalam bahasa Go, jauh lebih cepat dalam proses booting.

Flag `enable_lvm: true` memastikan Booster memuat modul LVM sebelum mencari root filesystem. Setelah konfigurasi selesai, jalankan `booster build` untuk mengkompilasi image initramfs baru.

```bash
nvim /etc/booster.yaml
```

Isi konfigurasi Booster:

```yaml
network:
  dhcp: on
universal: false
modules: -*,ext4
extra_files: fsck,fsck.ext4
strip: true
enable_lvm: true
```

Kemudian build image-nya:

```bash
cd /boot
ls /usr/lib/modules   # catat versi kernel yang tersedia

booster build --kernel-version [versi kernel] /boot/booster-linux-lts-new.img
rm -fr booster-linux-lts.img
```

---

### 15. Bootloader: systemd-boot

**systemd-boot** adalah boot manager yang sangat ringan dan dirancang khusus untuk firmware UEFI — ia membaca file teks sederhana untuk mengeksekusi kernel. `bootctl install` memasang file biner bootloader ke partisi EFI.

Kemudian buat file entri `booster.conf` yang memberitahu bootloader letak file Kernel Linux (`vmlinuz`), Initramfs Booster, dan lokasi root partisi. Terakhir, set entry ini sebagai default di `loader.conf`.

```bash
bootctl --path=/boot install
```

Buat file entri boot:

```bash
nvim /boot/loader/entries/booster.conf
```

```
title   arch with booster
linux   /vmlinuz-linux-lts
initrd  /intel-ucode.img
initrd  /booster-linux-lts-new.img
options root=/dev/proc/root rw
```

Set sebagai default:

```bash
nvim /boot/loader/loader.conf
```

```
default  booster.conf
```

Terakhir, perbarui bootloader:

```bash
bootctl --graceful update
```

---

## Selesai — Keluar & Reboot

Ketik `exit` untuk keluar dari lingkungan virtual arch-chroot dan kembali ke Live USB. Jalankan `umount -R /mnt` untuk melepaskan seluruh hirarki partisi secara rekursif — ini memastikan semua data ditulis ke disk dengan aman dan tidak korup. Terakhir, `reboot` untuk mulai ulang sistem.

```bash
exit
umount -R /mnt
reboot
```

> [!NOTE]
> Ingat untuk **mencabut flashdisk instalasi** saat logo BIOS/UEFI muncul, agar sistem boot dari harddisk yang baru dikonfigurasi — bukan dari Live USB.
