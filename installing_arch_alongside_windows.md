# Installing Arch alognside Windows
When trying to do so, I found it somewhat difficult. Reading many online formus, it seems that it's best to let `archinstall` manage an entire disk, defining its own partitions layout. However I didn't want to erase the Windows partition.

## Creating the partitions
You need the following clean partitions:
* A `EFI System` partition that need to be mounted at `/boot`,
* A `Filesystem` partition that is mounted at `/`.
* Optionally a `home` partition, to be mounted at `/home` to isolate the users files from the system files,
* Optionally a `swap` partition, to enable memory swapping and hybernation.
  
I used only the first two partition. As per swapping, I later defined a swap file.

These partitions can be created using Windows *Create and Manage partitions* utility, or `fdisk` utility before running `archinstall`

## Running archinstall
When running archinstall, make sure to format the `EFI System` using `FAT32` filesystem and set the mount point to `/boot`. The `Filesystem` partition, should instead be formatted with `ext4`, a more advanced filesystem.

## Post installation

Before rebooting, you should update the newly installed bootloader to detect the OSs on your machine. If you're using GRUB, run
```sh
sudo pacman -S ntfs-3g
sudo grub-mkconfig -o /boot/grub/grub.cfg
```
Regardeless of the boot loader you chose during the installation (I prefer GRUB), you need to tell your BIOS to boot from the `EFI System` partition just created.

I installed [arch-linux](https://k1ng.dev/distro-grub-themes/preview) theme using `grub-customizer`.