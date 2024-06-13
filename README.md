# Arch Linux Installaion

## Persiapan Instalasi 
1. **Koneksi Internet** : Pastikan host machine terhubung ke internet agar dapat mengakses dan mengunduh paket-paket yang diperlukan.
2. **Live USB Arch Linux**: Buat live USB Arch Linux yang dapat digunakan untuk booting ke sistem **Arch Linux**.
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

15. nano /etc/default/cpupower
    <details>
    <summary>Sebelum</summary>
    ```sh
    #governor=`performance`
    #min_freq="2600Mhz"
    #max-freq="2600Mhz"
    ```
    </details>

    <details>
    <summary>Sesudah</summary>
    ```sh
    governor=`performance`
    min_freq="2600Mhz"
    max-freq="2600Mhz"
    ```
    </details>