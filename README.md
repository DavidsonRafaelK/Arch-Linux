# Arch Linux Installaion

## Persiapan Instalasi 
1. **Koneksi Internet** : Pastikan host machine terhubung ke internet agar dapat mengakses dan mengunduh paket-paket yang diperlukan.
2. **Live USB Arch Linux**: Buat live USB Arch Linux yang dapat digunakan untuk booting ke sistem **Arch Linux**.[Download ISO]( https://archlinux.org/download/ ) image
3. **Rencana Partisi Disk**: Rencanakan partisi disk, misalnya:
   - [x] `/dev/vda1` untuk **EFI System Partition (ESP)** dengan `FAT32`.
   - [x] `/dev/vda2` untuk **root** partition dengan `ext4`.
4. **Waktu dan Ketelitian**: Proses instalasi membutuhkan ketelitian untuk mengikuti langkah-langkah dengan benar dan memastikan tidak ada kesalahan, terutama dalam memilih disk dan partisi yang benar.

## Tutorial Installasi 
### 1. Pilih server mirror terbaik dari Indonesia:
```sh
reflector --country 'Indonesia' --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
```
Perintah ini akan memperbarui daftar mirror di `/etc/pacman.d/mirrorlist` dengan server yang cepat dan **up-to-date**.

### 2. Lihat semua block devices di sistem:
```sh
lsblk
```
Perintah ini akan menampilkan daftar disk dan partisi yang ada, seperti `/dev/sda`, `/dev/vda`, dll.

### 3. Hapus semua tanda sistem file dari disk:
```sh
wipefs -a /dev/vda
```
**Catatan**: Sesuaikan `/dev/vda` dengan disk yang ingin digunakan (sesuai output dari `lsblk`).

### 4. Buat sistem file FAT32 pada partisi pertama:
```sh
mkfs.vfat -F 32 /dev/vda1
```
**Catatan**: Sesuaikan `/dev/vda1` dengan partisi pertama yang akan digunakan sebagai `EFI` System Partition (ESP).

### 5. Buat sistem file ext4 pada partisi kedua:
```sh
mkfs.ext4 /dev/vda2
```
**Catatan**: Sesuaikan `/dev/vda2` dengan partisi kedua yang akan digunakan sebagai **root partition**.

### 6. Mount partisi root:
```sh
mount /dev/vda2 /mnt
```
Perintah ini akan memasang partisi **root** ke direktori `/mnt`.

### 7. Buat direktori untuk EFI boot:
```sh
mkdir -p /mnt/boot/efi
```
Membuat direktori `/mnt/boot/efi` untuk me-mount partisi **EFI** nanti.

### 8. Buat direktori untuk home:
```sh
mkdir -p /mnt/home
```
Perintah ini membuat direktori `/mnt/home` untuk partisi **home** jika diperlukan di kemudian hari.

### 9. Mount partisi EFI:
```sh
mount /dev/vda1 /mnt/boot/efi
```
Perintah ini akan memasang partisi **EFI** ke direktori `/mnt/boot/efi`.

### 10. Buat direktori etc:
```sh
mkdir /mnt/etc
```
Membuat direktori `/mnt/etc` yang diperlukan untuk menyimpan file konfigurasi seperti `fstab`.

### 11. Buat file fstab:
```sh
genfstab -U -p /mnt >> /mnt/etc/fstab
```
Perintah ini menghasilkan file `/mnt/etc/fstab` yang berisi entri untuk semua partisi yang di-mount, menggunakan UUID dan path perangkat.

### 12. Instal paket-paket dasar:
```sh
pacstrap -K /mnt base linux linux-firmware
```
Perintah ini menginstal sistem dasar Arch Linux (base system, kernel Linux, dan firmware Linux) ke dalam partisi yang di-mount di `/mnt`.

### 13. Mengubah Root ke Sistem Baru
```sh
arch-chroot /mnt
```
Perintah ini mengubah root ke sistem baru yang telah diinstal di `/mnt`, memungkinkan konfigurasi lebih lanjut dan instalasi paket-paket tambahan.

### 14. Menginstal Paket-Paket Tambahan
```sh
pacman -S amd-ucode sof-firmware grub sudo efibootmgr base-devel git nano cpupower xorg xorg-xinit pulseaudio pavucontrol
```
Perintah ini menginstal berbagai paket tambahan yang diperlukan untuk sistem, termasuk microcode untuk prosesor AMD, firmware untuk perangkat audio, bootloader GRUB, dan berbagai utilitas dan aplikasi dasar lainnya. **( Install sesuai preferensi dan kebutuhan anda )**.

### 15. nano /etc/default/cpupower
```sh
nano /etc/default/cpupower
```
Anda akan mengubah pengaturan default cpupower untuk mengoptimalkan performa prosesor.

<details>
<summary>Sebelum</summary>

```
#governor=`ondemand` 
#min_freq="2.25Ghz" 
#max-freq="3Ghz"
```
</details>

<details>
<summary>Sesudah</summary>

```
governor=`performance` 
min_freq="2600Mhz" 
max-freq="2600Mhz"
```
</details>
Pengaturan di atas akan mengatur governor ke `performance` dan menetapkan frekuensi minimum dan maksimum ke 2600MHz.

### 16.Mengaktifkan cpupower Service
Aktifkan cpupower service agar berjalan otomatis saat boot:
```sh
systemctl enable cpupower
```
Perintah ini memungkinkan cpupower untuk mengatur frekuensi prosesor sesuai dengan konfigurasi yang telah Anda tentukan.

### 17. Membuat kata sandi untuk Root
```sh
passwd
```

### 18. Menambahkan Pengguna Baru
```sh
useradd -m -g users -G wheel mamaUser
```
Anda membuat pengguna baru `mamaUser` dengan grup utama `users` dan menambahkannya ke grup `wheel` untuk memiliki akses **sudo**.

### 19. passwd (namaUser)
```sh
passwd mamaUser
```
Ini akan meminta Anda untuk mengatur kata sandi untuk pengguna baru (mamaUser) yang telah ditambahkan sebelumnya.
### 20. Mengedit File sudoers
Anda akan mengubah konfigurasi **sudoers** untuk memberikan akses **sudo** kepada pengguna dalam grup `wheel`.

<details>
<summary>Sebelum</summary>

```
#wheel ALL=(ALL:ALL) ALL
```
<details>
<summary>Sesudah</summary>

```
wheel ALL=(ALL:ALL) ALL
```
Ini akan mengaktifkan akses **sudo** untuk pengguna dalam grup `wheel`.
21. Instalasi GRUB Bootloader
Instal GRUB bootloader untuk UEFI dengan nama label "Arch Linux":
```sh
grub-install --target=x86_64-efi --bootloader-id="Arch Linux" --recheck
```
Perintah ini akan menginstal GRUB bootloader pada partisi EFI dengan label "Arch Linux".

