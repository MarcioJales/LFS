# LFS

Describe the steps I take to build my Linux from scratch: http://www.linuxfromscratch.org/lfs/

Version 10.1: https://www.linuxfromscratch.org/lfs/view/10.1/

## Host System

- Ubuntu 20.04;
- x86_64.

After executing `version_check.sh`, from section 2.2, I had to install the following packages: *gcc*, *g++*, *make*, *bison*, *gawk*.

Besides, I've changed to link of `/bin/sh`:

```
$ sudo rm /bin/sh
$ sudo ln -s /bin/bash /bin/sh
```

The script passes afterwards, with suitable versions:

```
bash, version 5.0.17(1)-release
/bin/sh -> /usr/bin/bash
Binutils: (GNU Binutils for Ubuntu) 2.34
bison (GNU Bison) 3.5.1
/usr/bin/yacc -> /usr/bin/bison.yacc
bzip2,  Version 1.0.8, 13-Jul-2019.
Coreutils:  8.30
diff (GNU diffutils) 3.7
find (GNU findutils) 4.7.0
GNU Awk 5.0.1, API: 2.0 (GNU MPFR 4.0.2, GNU MP 6.2.0)
/usr/bin/awk -> /usr/bin/gawk
gcc (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0
g++ (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0
(Ubuntu GLIBC 2.31-0ubuntu9.2) 2.31
grep (GNU grep) 3.4
gzip 1.10
Linux version 5.8.0-59-generic (buildd@lcy01-amd64-022) (gcc (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0, GNU ld (GNU Binutils for Ubuntu) 2.34) #66~20.04.1-Ubuntu SMP Thu Jun 17 11:14:10 UTC 2021
m4 (GNU M4) 1.4.18
GNU Make 4.2.1
GNU patch 2.7.6
Perl version='5.30.0';
Python 3.8.10
sed (GNU sed) 4.7
tar (GNU tar) 1.30
texi2any (GNU texinfo) 6.7
xz (XZ Utils) 5.2.4
g++ compilation OK
```

## Create LFS partitions

Section 2.4 states guidelines to create LFS partitions.

Using `fsdisk`, we can see the current disk state:

```
# As root
$ fdisk /dev/sda
...

Command (m for help): p
Disk /dev/sda: 931,53 GiB, 1000204886016 bytes, 1953525168 sectors
Disk model: ST1000LM024 HN-M
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: 81D78C68-836D-4653-8C78-ED4F887F1701

Device       Start        End    Sectors  Size Type
/dev/sda1     2048     999423     997376  487M EFI System
/dev/sda2   999424    1001471       2048    1M BIOS boot
/dev/sda3  1001472    2052095    1050624  513M Microsoft basic data
/dev/sda4  2052096 1172272799 1170220704  558G Linux filesystem
```

We have also unpartitioned space:

```
Command (m for help): F

Unpartitioned space /dev/sda: 372,54 GiB, 400001015296 bytes, 781251983 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (loCommand (m for help): F

Unpartitioned space /dev/sda: 372,54 GiB, 400001015296 bytes, 781251983 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes

     Start        End   Sectors   Size
1172273152 1953525134 781251983 372,5Ggical/physical): 512 bytes / 4096 bytes

     Start        End   Sectors   Size
1172273152 1953525134 781251983 372,5G
```

We do not create any EFI partition, since the driver is the same from the host system. Thus, we use the same partition table.

Finally, partitions were created as below:

```
Command (m for help): n       
Partition number (5-128, default 5): 11
First sector (1172272800-1953525134, default 1172273152): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (1172273152-1953525134, default 1953525134): +1G

Created a new partition 11 of type 'Linux filesystem' and of size 1 GiB.

Command (m for help): n
Partition number (5-10,12-128, default 5): 18
First sector (1174370304-1953525134, default 1174370304): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (1174370304-1953525134, default 1953525134): +8G

Created a new partition 18 of type 'Linux filesystem' and of size 8 GiB.

Command (m for help): n
Partition number (5-10,12-17,19-128, default 5): 12
First sector (1191147520-1953525134, default 1191147520): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (1191147520-1953525134, default 1953525134): +200G

Created a new partition 12 of type 'Linux filesystem' and of size 200 GiB.

Command (m for help): n
Partition number (5-10,13-17,19-128, default 5): 13
First sector (1610577920-1953525134, default 1610577920): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (1610577920-1953525134, default 1953525134): 

Created a new partition 13 of type 'Linux filesystem' and of size 163,5 GiB.

Command (m for help): t
Partition number (1-4,11-13,18, default 18): 18
Partition type (type L to list all types): 19

Changed type of partition 'Linux filesystem' to 'Linux swap'.
```

We may check the partitions:

```
# fdisk -l /dev/sda
Disk /dev/sda: 931,53 GiB, 1000204886016 bytes, 1953525168 sectors
...

Device          Start        End    Sectors   Size Type
...
/dev/sda11 1172273152 1174370303    2097152     1G Linux filesystem
/dev/sda12 1191147520 1610577919  419430400   200G Linux filesystem
/dev/sda13 1610577920 1953525134  342947215 163,5G Linux filesystem
/dev/sda18 1174370304 1191147519   16777216     8G Linux swap
```

The mapping intended is:


- `/dev/sda11` for `/boot`;
- `/dev/sda12` for `/`;
- `/dev/sda13` for `/home`;
- `/dev/sda18` for swap.