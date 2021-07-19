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