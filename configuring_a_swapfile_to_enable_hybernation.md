# How to swap on file and enable hybernation

If you didn't specify a swap partition when installing your operating system, *virtual memory* and *hybernation* functionalities won't work.

To allocate a swap space, you can either make a **swap partition** or a **swap file**. In this guide I will use the latter strategy, but the difference is subtle: a wap partition has its own *UUID*, while the *UUID* of the swap file is that of the partition it resides in.

## Allocate space for the swap file
Remember, since hybernation dumps main memory to secondary memory, **the swap space should be at least as large as the main memory**. In my case, I have *16GiB* of main memory, so I will allocate *16Gib* of space.

I'm not sure myself what *zram* is, but I know that later `archinstall` versions enable it by default, and it caused me problems when trying to recover from hibernation.

There are different way to create the `/swapfile` file, here I'll use `dd`.

```sh
sudo swapoff -a           # turn off swap and disable zram if present
sudo rm -i /swapfile      # remove any old /swapfile

sudo dd if=/dev/zero of=/swapfile bs=1M count=<YOUR_RAM_SIZE_IN_MB> # allocate the /swapfile

sudo chmod 600 /swapfile  # set proper file protections
sudo mkswap /swapfile     # init /swapfile
sudo swapon /swapfile     # turn on swap
free -h                   # verify the presence of the swap file
```

## Automatically mount the swap file at startup
Now you have to tell `fstab` the presence of the swapfile.

```sh
sudo nano /etc/fstab
```
Add `/swapfile none swap sw 0 0` on the bottom of the file. Note that `/swapfile` is a special *UUID* that acts as a placeholder since we have a file and not a partition. If we were to allocate the swap space of a partition, this would be a proper *UUID*.

## Tell GRUB where the swap file is
Here I assume you use *GRUB* as your bootloader. If that's not the case, you need to search somewhere else what to do from here on.

### Find the resume_offset
> sudo nano /etc/default/grub
As I said earlier, since I am using a swap file, the `resume=UUID=` must point to the *UUID* of the partition where the file is located. But the first block of the file isn't at the beginning of the partition, thus we need to find its offset.

Run the following command.
```sh
sudo filefrag -v /swapfile
```
You fill find the offset of the first physical block in the first value of the `physical_offset` column. In my example, such value is $3713024$.

```txt
Filesystem type is: ef53
File size of /swapfile is 17179869184 (4194304 blocks of 4096 bytes)
 ext:     logical_offset:        physical_offset: length:   expected: flags:
   0:        0..   88063:    3713024..   3801087:  88064:            
   1:    88064..   90111:   15540224..  15542271:   2048:    3801088:
   2:    90112..   92159:   15546368..  15548415:   2048:   15542272:
   3:    92160..   94207:   30660608..  30662655:   2048:   15548416:
   4:    94208..   96255:     509952..    511999:   2048:   30662656:
   ...
```

### Update GRUB config

```sh
sudo nano /etc/default/grub
```

Append at the end of the `GRUB_CMDLINE_LINUX_DEFAULT` string `resume=UUID=<UUID_OF_PARTITION_WHERE_SWAPFILE_IS> resume_offset=<SWAP_OFFSET>`, where the `SWAP_OFFSET` is the number just found.

Update the grub config.

```sh
sudo grub-mkconfig -o /boot/grub/grub.cfg
```