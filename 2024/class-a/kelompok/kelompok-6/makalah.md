**<h1 align="center">INSTALASI DESKTOP ARCH LINUX </h1>**

<p align="center"><small>Makalah ini disusun untuk memenuhi tugas mata kuliah Perpustakaan dan Arsip Digital

<p align="center"><img width="690" height="599" alt="690px-logouinsyarifhidayatullahjakarta" src="https://github.com/user-attachments/assets/656b29bd-4701-4b2d-8d57-e0c13afece40" />

**Dosen Pengampu:**

Al Muhdil Karim, S.IP, M.Hum.

**Disusun Oleh Kelompok 6:**

1. Kyshya Kanaya (12402051010006)

2. Nayla Rizky Arsi Ainun (12402051030038)

3. Dhea Azzahra Putri (12402051050113)

<p align="center"><small>Kelas A

<p align="center"><b>PROGRAM STUDI ILMU PERPUSTAKAAN</b></p>

<p align="center"><b>FAKULTAS ADAB DAN HUMANIORA</b></p>

<p align="center"><b>UNIVERSITAS ISLAM NEGERI SYARIF HIDAYATULLAH JAKARTA</b></p>

<p align="center"><b>TAHUN 2026</b></p>

---

# KATA PENGANTAR

Assalamu’alaikum Wr. Wb. Puji syukur kehadirat Allah SWT, Tuhan Yang Maha Pengasih lagi Maha Penyayang, atas segala rahmat, hidayah, dan karunia-Nya yang senantiasa menyertai kami sehingga makalah Instalasi Desktop Arch Linux ini dapat terselesaikan dengan baik. Penyusunan makalah ini dimaksudkan untuk memenuhi salah satu tugas pada mata kuliah Perpustakaan dan Arsip Digital. Selain itu, kami berharap karya tulis ini dapat memberikan kontribusi positif dalam memperluas pengetahuan, baik bagi pembaca maupun bagi kami pribadi.

Ucapan terima kasih yang tulus kami sampaikan kepada Bapak Al Muhdil Karim, S. IP, M. Hum. selaku dosen pengampu mata kuliah Perpustakaan dan Arsip Digital. Kami  menyadari sepenuhnya bahwa makalah ini masih memiliki banyak kekurangan dan jauh dari kata sempurna. Akhir kata, semoga makalah ini memberikan manfaat bagi pengembangan ilmu perpustakaan di Indonesia.

Wassalamu’alaikum Wr. Wb.

# BAB I PENDAHULUAN

## 1.1 Latar Belakang

Arch Linux adalah distribusi GNU/Linux serbaguna x86-64 yang dikembangkan secara independen dan berupaya menyediakan versi stabil terbaru dari sebagian besar perangkat lunak dengan mengikuti model rilis bergulir (rolling release) . Komunitas Arch telah tumbuh dan matang menjadi salah satu distribusi Linux yang paling populer dan berpengaruh, yang juga dibuktikan oleh perhatian dan ulasan yang diterima selama bertahun-tahun. Selain beberapa pengecualian tertentu, para pengembang Arch tetap merupakan sukarelawan paruh waktu yang tidak dibayar, dan tidak ada prospek untuk memonetisasi Arch Linux, sehingga akan tetap gratis dalam segala arti kata. 

dalam  menginstall desktop Arch Linux terdapat beberapa komponen penting yang harus di pasang. Komponen  itu terdiri dari NetworkManager adalah program yang menyediakan deteksi dan konfigurasi untuk sistem agar dapat terhubung ke jaringan secara otomatis; KDE Plasma proyek perangkat lunak yang saat ini terdiri dari lingkungan desktop; PipeWire adalah kerangka kerja multimedia tingkat rendah yang baru. Tujuannya adalah untuk menawarkan perekaman dan pemutaran audio dan video dengan latensi minimal dan dukungan untuk aplikasi berbasis PulseAudio , JACK , ALSA , dan GStreamer; Dolphin adalah pengelola file bawaan KDE; Kitty adalah emulator terminal berbasis OpenGL yang dapat diprogram dengan fitur TrueColor, dukungan ligatur, ekstensi protokol untuk input keyboard, dan rendering gambar. Ia juga menawarkan kemampuan tiling, seperti GNU Screen atau tmux. Dengan begitu, materi ini bertujuan untuk memberikan penjelasan tentang proses installasi desktop Arch Linux sehingga kita dapat mengetahui bagaimana sebuah sistem desktop Linux dibangun. 

## 1.2 Rumusan Masalah

- Baigamana langkah instalasi NetworkManager?

- Baigamana langkah instalasi Plasma?

- Baigamana langkah instalasi Pepwire?

- Baigamana langkah instalasi Dolphin?

- Baigamana langkah instalasi Kitty?

## 1.3 Tujuan Masalah

- Menjelaskan langkah instalasi NetworkManager.

- Menjelaskan langkah instalasi Plasma.

- Menjelaskan langkah instalasi Pepwire.

- Menjelaskan langkah instalasi Dolphin.

- Menjelaskan langkah instalasi Kitty.
  
# BAB II PEMBAHASAN

## 2.1 NetworkManager
NetworkManager adalah program yang digunakan untuk mendeteksi dan mengatur konfigurasi jaringan agar sistem dapat terhubung ke internet secara otomatis, baik melalui jaringan kabel maupun nirkabel. Pada jaringan wireless, NetworkManager dapat memprioritaskan jaringan yang sudah dikenal dan secara otomatis berpindah ke jaringan yang lebih stabil, sedangkan pada jaringan kabel layanan ini akan lebih diprioritaskan dibandingkan koneksi wireless. Selain itu, NetworkManager juga mendukung koneksi modem dan beberapa jenis VPN sehingga memudahkan pengguna dalam mengelola berbagai jenis koneksi jaringan pada sistem Linux.

## 2.2 Plasma
KDE Plasma merupakan proyek perangkat lunak yang terdiri dari lingkungan desktop KDE Plasma, berbagai aplikasi KDE Applications, serta pustaka tambahan Qt yang disebut KDE Frameworks. KDE Plasma dikenal sebagai desktop environment yang modern, ringan, dan memiliki tampilan antarmuka yang menarik sehingga banyak digunakan pada sistem Linux desktop. Penjelasan Installasi KDE Plasma:

1. Pertama-tama pastinya kita akan masuk ke Arch Linux terlebih dahulu, Jangan lupa untuk selalu memeriksa updetan terbaru

``` sudo pacman -syyu ```

2. Lalu kemudian akan melakukan installasi KDE Plasma

``` sudo pacman -s xorg plasma plasma-wayland-sesion- kde-applications ```

setelah sudah semua, cukup all saja dan default 1, dan yang terakhir y

3. Setela itu aktifkan SDDM, SDDM ini merupakan display manager dari KDE Plasma

``` sudo systemsti enable sddm.service ``` kemudian kita start ``` sudo systemcti start sddm.service ```

## 2.3 Pipwire
PipeWire merupakan kerangka kerja multimedia tingkat rendah yang digunakan untuk mengelola audio dan video pada sistem Linux. Layanan ini juga dapat dikonfigurasi sebagai server audio maupun server penangkap video. Selain itu, PipeWire mendukung penggunaan container seperti Flatpak dan menggunakan sistem keamanan berbasis izin seperti Polkit untuk mengatur akses perekaman layar maupun audio sehingga lebih aman dan fleksibel digunakan pada desktop Linux modern. 

1. Instalasi PipeWire menggunakan

   ``` sudo pacman -S pipewire ```. 

2. Jika ingin install PipeWire beserta komponen pendukungnya gunakan
   
   ``` sudo pacman -S pipewire wireplumber pipewire-pulse pipewire-alsa pipewire-jack ```

3. Setelah instalasi selesai, aktifkan layanan PipeWire, PulseAudio dan WirePlumber.

   ``` systemctl --user enable --now pipewire.service ```

   ``` systemctl --user enable --now pipewire-pulse.service ```

   ``` systemctl --user enable --now wireplumber.service ```

5. Untuk memastikan PipeWire berjalan dengan baik gunakan
   
   ``` pactl info ```. Jika instalasi berhasil maka akan muncul tulisan ``` Server Name: PulseAudio (on PipeWire) ``` , tulisan tersebut menunjukkan bahwa PipeWire telah aktif dan berfungsi sebagai sistem audio utama pada Arch Linux.

## 2.4 Dolphin
Dolphin merupakan file manager bawaan KDE yang digunakan untuk mengelola file dan folder pada sistem Linux.  Selain itu, Dolphin juga mendukung fitur preview file untuk video, gambar, PDF, audio, dan berbagai format lainnya melalui paket tambahan seperti ffmpegthumbs dan kdegraphics-thumbnailers. Penjelasan Installasi KDE Plasma:

1. Install Dolphin

``` sudo apt install dolphin ``` tunggu hingga installasi selesai 

2. Seletah semuanya selesai buka Doplphin File Manager
   
## 2.5 Kitty
Kitty merupakan emulator terminal berbasis OpenGL yang dapat diprogram dan mendukung fitur TrueColor, ligatur, ekstensi protokol input keyboard, serta rendering gambar di dalam terminal. Kitty juga memiliki kemampuan tiling seperti GNU Screen atau tmux sehingga pengguna dapat membuka dan mengatur beberapa tab maupun jendela terminal dengan mudah melalui kombinasi tombol keyboard. Selain itu, Kitty menyediakan fitur tambahan yang disebut kittens, yaitu subprogram untuk berbagai kebutuhan seperti menampilkan gambar di terminal (icat), membandingkan file (diff), serta mengakses clipboard sistem (clipboard).

1. Install Kitty menggunakan

   ``` sudo pacman -S kitty ```

2. Setelah instalasi selesai, jalankan Kitty menggunakan
  
   ``` kitty ```

# BAB III PENUTUP

## 3.1 Kesimpulan

# DAFTAR PUSTAKA

Arch Linux - ArchWiki. (2025, Desember 28). https://wiki.archlinux.org/title/Arch_Linux

Dolphin - ArchWiki. (2026, April 19). https://wiki.archlinux.org/title/Dolphin

KDE - ArchWiki. (2026, Mei 15). https://wiki.archlinux.org/title/KDE

Kitty - ArchWiki. (2026, April 10). https://wiki.archlinux.org/title/Kitty

NetworkManager - ArchWiki. (2026, Mei 10). https://wiki.archlinux.org/title/NetworkManager

PipeWire - ArchWiki. (2026, Mei 8). https://wiki.archlinux.org/title/PipeWire
