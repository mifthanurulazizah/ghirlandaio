### ini adalah gudie installasi linux berdasarkan pemahaman dan pengalaman langsung penulis Achmad Zanuarta Pattisahusiwa 
#### installasi ini akan mengikuti berdasarkan arch linux wiki https://wiki.archlinux.org/title/Installation_guide, dan sesuai kebutuhan pengguna 
##### konsep arch wiki itu adalah build your own way, jadi arch wiki itu costumizeable dan sesuai dengan jalan dan prinsip dari pengguna dan tergantung dengan keinginan pengguna itu sendiri 
>[!info] step by step yang akan di ikuti: <br>
> [1] mendowload ISO, menentukan UEFI bootloader apakah rufus atau balenaetcher untuk UEFI boot loadernya <br>
> [2] mengecek format UEFI apakah 64 atau 32 UEFI <br>
> [3] mengecek bentuk dari partisi, apakah nvme , sda , atau bentuk lain <br>
> [4] membagi partisi dan membentuk format juga melakukan mount <br>
> [5] koneksi ke internet <br> 
> [6] pacstrap untuk mengambil packages penting untuk berjalannya linux <br> 
> [7] genfstab untuk menentukan partisimana yang akan di mount duluan ketika melakukan boot <br> 
> [8] arch-chroot /mnt dan configurasi juga berbagaimacam penyesuaikan di dalamnya <br> 
>[9]  dari menamai device, menyesuaikan network, bahasa, waktu, pemilihan dekstopenvironment, pembuatan mkinitcpio dan penentuan UEFI boot loader / image loader dan managernya untuk boot linux <br>

 ### mendownload iso dan menentukan live boot UEFI loader
 <img width="1761" height="432" alt="image" src="https://github.com/user-attachments/assets/a1dccf6a-81c8-4aa5-9d0f-6743ada396a1" />

 <img width="1737" height="144" alt="image" src="https://github.com/user-attachments/assets/04e1327c-d10e-454e-b4eb-8c4070320995" />
pertama kalian cari cara untuk download arch linxu kalian dengan masuk ke dalam site https://archlinux.org/download/
kemudian cari mirror untuk arch linux versi terbaru dari ISO nya setelah terinstall maka selanjutnya adalah pemilihan untuk live boot nya 
antara rufus atau balena etcher , saya akan memilih menggunakan balenaetcher karena bentuk dan fungsionalitasnya bisa untuk berbagai macam jenis OS <br> 

<img width="839" height="534" alt="Screenshot_20260516_005208" src="https://github.com/user-attachments/assets/a88f785b-3d5e-420d-adcb-fdfb4e72a3f6" /> <br> 

seperti di contoh masukan sama ISO dan flashdisk kalian ke dalam agar isi flashdisk kalian akan di tulis ulang untuk masuk boot bootloader environment dari linux 
reboot kemudian tekan f12 berulang kali untuk masuk ke live environment boot, jangan lupa untuk mematikan security boot sebelum instalasi linux karena boot live inevrionment tidak akan berkerja jika security pada bootloader masih aktif, kemudian exit 

## setelahnya masuk pada boot environmet untuk instalasi linux 

#### connect to internet  <br> 
> [1]  ketik ip link untuk melihat apakah status dari internet contoh : __wlan0__ itu aktif atau tidak / up or down <br> 
> [2] ketik __iwctl__ untuk masuk ke dalam iwd <br> 
> [3] ketik __device list__ untuk menunjukan device akan menunjukan contoh : wlan0 <br> 
> [4] scan internet dengan  station wlan0 dan get-networks untuk mendapatkan network di sekitar anda <br> 
> [5] kemudian __station wlan0 connect__ [nama_wifi] , masukan pharaprashe atau password : [password 12345] <br> 
> [6]  setelahnya ketik __station wlan0 show__ untuk menunjukan status <br> 
> [7]  ping untuk mengecek apakah internet sudah berkerja dengan ping google.com <br> 
> [8] tekan __ctrl + c__ untuk menghentikan terhadap ping <br>

##### menentukan partisi mana yang ingin di boot  
> ketik __fdisk -l__ untuk mengetahui isi dari partisi dan bentuk juga type atau jenis formatnya <br>
> ketik __lsblk__ untuk melihat partisi mana yang sudah di mount dan mana yang belum <br>

#### __contoh idealnya dan penjelasannya__
nvmeon1 / sda 1 adalah partisi yang akan kalian edit nantinya contoh bentuk lainnya adalah /dev/sda, /dev/nvme0n1 or /dev/mmcblk0

contoh : 

| nvmeon1|bentuk format|bentuk mount|besaran partisi| 
|:----------|:-----------:|:----------:|-------------:|
|/dev/ nvme0n1p1|mkfs.vfat| /mnt/boot|5/ 2 G |
| /dev/ nvme0n1p2|mkfs.ext4| / |50 /20 G| 

<br> 
contoh milik saya adalah : 

|Partisi | bentuk format| mount| besaran | alasan | 
|:-------|:------------|:---|:--------|:-------|
|nvme0n1p1|mkfs.vfat | /mnt/boot | 5G | untuk UEFI bootloader dari linux |
|nvme0n1p2| mkfs.ext4 | /mnt | 50G | untuk root dan berbagai sistem yang berkaitan dengan root |
|nvme0n1p3| mkfs.ext4 | /mnt/home | seluruh sisa partisi yang ada | untuk file home dan jenis file lain di direktori home| 

bentuk lain bisa di kostumisasi sesuai keingina seperti __root /dev/nvmeon1p2 dengan /mnt__ dan misal __/dev/nvme0n1p3 dengan /dev/nvme0n1p3 /mnt/home__ 
user atau anda juga dapat menentukan file swap fungsinya apa ?, itu optional di gunakan jika ram tidak cukup maka file akan menggambil dari partisi disk untuk di swap agar ram tidak penuh 

##### jika anada melakukan dual boot 
> [1] jika anda melakukan __dual boot__ maka anda wajib untuk mengatur partisi anda terlebih dahulu melalu __WIndows + R  ketik diskmgmt.misc__ <br>
> [2] setelahnya atur besaran partisi sesuai dengan kebutuhan anda , __unalocated space__ yang anda buat akan di naggap sebagai freespace yang akan di gunakan sebagai partisi baru untuk linuc <br> 
> [3] __CAUTION__, jika anda adalah pengguna windows maka format dan partisi windows akan di tulis dalam bentuk __NTFS__ dan format lain yang akan ada nanti ketika kita ingin mengganti atau mengubah menggunakan __cfdisk__ maka format type NTFS tidak di sentuh <br>
> di dalam __cfdisk__ bagian ini anda dapat mengedit free space yang telah anda alokasikan dengan pilih free space pilih new dan sesuai kan bentuk partisinya,tentukan type nya, pilih write untuk commit and quit untuk tahap selanjutnya <br> 

#### jika tidak melakukan dualboot 
> [1] anda bebas dalam menyesuiakan partisi di dalam sesi ini, menyesuaikan partisi boot,root, dan home untuk partisi anda, tentukan type, setelahnya write baru quit. <br> 

##### membuat format untuk partisi [ mkfs, mkdir , mount , umount ] 

mkfs. <br> 
>untuk boot : mkfs.fat -F32 /dev/nama_partisi_untuk _efi /mnt/boot <br> 

>untuk root : mkfs.ext4 /dev/nama_partisi_untuk_root /mnt <br> 

>untuk home : mkfs.ext4 /dev/nama_partisi_untuk_home /mnt/home

[!info] mengapa harus FAT -F32 ? , karena linux harus menggunakan -F32 , mengapa menggunakan ext4 ? , karena ini yang lebih modern dan stabil dalam format sistemnya

mount sekaligus mkdir <br> 

> mount --mkdir /dev/nama_partisi_EFI /mnt/boot <br> 
> mount --mkdir /dev/nama_partisi_home /mnt/home <br>

otomatis __membuat direktori dengan mkdir__ dan __mount point__ pada partisi yang ingin di mount pada __/mnt/boot__ dan __/mnt/home__

### pacstrap /mnt 

>di gunakan untuk menclone atau mengambil packages penting dari mirror repo archlinux <br> 
>dengan cara __Pacstrap -K /mnt__ <br>
>gunakan amd-ucode untuk AMD <br> 
>gunakan intel-ucode untuk INTEL <br>
>Pacstrap -K /mnt base base-devel linux linux-firmware git neovim iwd <br>

base dan linux-firmware itu penting untuk menjalankan linux base adalah dasar atau inti dari repo linux sebagai dasar dasar dari sistem linux dan linux-firmware = firmware untuk linux 
neovim adalah teks editor untuk linux dan iwd adalah sistem network untuk linux 

### CP network configuration 

mkdir -p /var/lib/iwd 


















