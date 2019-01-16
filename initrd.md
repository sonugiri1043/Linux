# Linux initial RAM disk (initrd) overview


## What's an initial RAM disk?

The initial RAM disk (initrd) is an initial root file system that is mounted prior to when the real root file system
is available. The initrd is bound to the kernel and loaded as part of the kernel boot procedure. The kernel then mounts
this initrd as part of the two-stage boot process to load the modules to make the real file systems available and get at
the real root file system.

The initrd contains a minimal set of directories and executables to achieve this, such as the insmod tool to install kernel
modules into the kernel.

In the case of desktop or server Linux systems, the initrd is a transient file system. Its lifetime is short, only serving
as a bridge to the real root file system. In embedded systems with no mutable storage, the initrd is the permanent root file
system. This article explores both of these contexts.

Default Linux initrd directory structure

# ls -la
#

drwxr-xr-x  10 root root	4096 May 7 02:48 .

drwxr-x---  15 root root	4096 May 7 00:54 ..

drwxr-xr-x  2  root root	4096 May 7 02:48 bin

drwxr-xr-x  2  root root	4096 May 7 02:48 dev

drwxr-xr-x  4  root root	4096 May 7 02:48 etc

-rwxr-xr-x  1  root root 	812 May 7 02:48 init

-rw-r--r--  1  root root 1723392 May 7 02:45 initrd-2.6.14.2.img

drwxr-xr-x  2  root root	4096 May 7 02:48 lib

drwxr-xr-x  2  root root	4096 May 7 02:48 loopfs

drwxr-xr-x  2  root root	4096 May 7 02:48 proc

lrwxrwxrwx  1  root root   	3 May 7 02:48 sbin -> bin

drwxr-xr-x  2  root root	4096 May 7 02:48 sys

drwxr-xr-x  2  root root	4096 May 7 02:48 sysroot

The small, but necessary, set of applications are present in the ./bin directory, including nash
(not a shell, a script interpreter), insmod for loading kernel modules, and lvm (logical volume manager tools).

Of interest in Listing 3 is the init file at the root. This file, like the traditional Linux boot process,
is invoked when the initrd image is decompressed into the RAM disk


## Diskless Boot
Much like embedded booting scenarios, a local disk (floppy or CD-ROM) isn't necessary to boot a kernel and
ramdisk root filesystem. The Dynamic Host Configuration Protocol (or DHCP) can be used to identify network
parameters such as IP address and subnet mask. The Trivial File Transfer Protocol (or TFTP) can then be used
to transfer the kernel image and the initial ramdisk image to the local device. Once transferred, the Linux kernel
can be booted and initrd mounted, as is done in a local image boot.

For more info see: https://www.ibm.com/developerworks/library/l-initrd/index.html
