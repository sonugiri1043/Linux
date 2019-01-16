## Kernel modules

A long time ago, computer operating systems were configured and built to handle a specific set of attached hardware. If a device was not built into the operating system kernel, it could not be added or used. As the variety of devices increased and as the need grew to dynamically add devices by hot plugging, this model no longer worked. Today the kernel is minimal, and device support is configured by loadable kernel modules (LKMs). A basic set of device-support modules is usually included in an initial RAM disk that is loaded as part of the boot process. Once the system is up, the kernel probes the system further and loads needed device modules as new devices are discovered.

Several commands are used for interrogating and manipulating kernel modules. I'll cover lsmod, modinfo, and modprobe here. The modprobe command has now replaced the function of the earlier insmod and rmmod commands because it is more powerful.


### Using lsmod

The lsmod command formats the information from /proc/modules to give you a current status of the modules in your Linux system. The lsmod command has no options. Listing 1 shows a partial listing of the module status on my system.


##### **Listing 1. Partial listing of module status with lsmod` \
[ian@atticf22 ~]$ lsmod  \
Module                  Size  Used by \
vfat                   24576  1 \
fat                    69632  1 vfat \
uas                    24576  0 \
usb_storage            65536  2 uas \
xt_CHECKSUM            16384  1 \
ipt_MASQUERADE         16384  3 \
nf_nat_masquerade_ipv4    16384  1 ipt_MASQUERADE \
nf_conntrack_netbios_ns    16384  0 \
nf_conntrack_broadcast    16384  1 nf_conntrack_netbios_ns \
ip6t_rpfilter          16384  1 \
ip6t_REJECT            16384  2 \
nf_reject_ipv6         16384  1 ip6t_REJECT \
xt_conntrack           16384  22 \
ebtable_nat            16384  1 \
ebtable_broute         16384  1 \
ebtable_filter         16384  1 \
ebtables               32768  3 ebtable_broute,ebtable_nat,ebtable_filter \
ip6table_nat           16384  1 \
nf_conntrack_ipv6      20480  12 \
nf_defrag_ipv6         36864  1 nf_conntrack_ipv6 \
nf_nat_ipv6            16384  1 ip6table_nat \
ip6table_mangle        16384  1 \
ip6table_security      16384  1 \
ip6table_raw           16384  1 \
ip6table_filter        16384  1 \
ip6_tables             28672  5 ip6table_filter,ip6table_mangle,ip6table_security,ip6table_nat, \
                                ip6table_raw \
iptable_nat            16384  1 \
nf_conntrack_ipv4      16384  12 \
nf_defrag_ipv4         16384  1 nf_conntrack_ipv4 \
nf_nat_ipv4            16384  1 iptable_nat \
nf_nat                 28672  3 nf_nat_ipv4,nf_nat_ipv6,nf_nat_masquerade_ipv4 \
nf_conntrack          106496  9 nf_conntrack_netbios_ns,nf_nat,nf_nat_ipv4,nf_nat_ipv6,xt_conntrack, \
                                nf_nat_masquerade_ipv4,nf_conntrack_broadcast,nf_conntrack_ipv4, \
                                nf_conntrack_ipv6 \
iptable_mangle         16384  1 \
iptable_security       16384  1 \
iptable_raw            16384  1 \
bnep                   24576  2 \
bluetooth             491520  5 bnep \
rfkill                 24576  2 bluetooth \
fuse                   94208  3 \
tun                    28672  1 \
bridge                114688  1 ebtable_broute \
ppdev                  20480  0 \
uvcvideo               90112  0 \
kvm_amd                65536  0 \
kvm                   495616  1 kvm_amd \
videobuf2_vmalloc      16384  1 uvcvideo \
videobuf2_core         49152  1 uvcvideo \
videobuf2_memops       16384  1 videobuf2_vmalloc \
v4l2_common            16384  1 videobuf2_core \
videodev              159744  3 uvcvideo,v4l2_common,videobuf2_core \
media                  24576  2 uvcvideo,videodev \
snd_usb_audio         180224  3 \
k10temp                16384  0 \
btrfs                 974848  0 \
edac_core              53248  0 \
`**


### Using modinfo

Use the modinfo command to get information about a module. You can give a full filename or just the module name.Listing 2 shows information for the vfat module, which handles the various forms of FAT-formatted drives.


##### **Listing 2. Information about the vfat module` \
[ian@atticf22 ~]$ modinfo vfat \
filename:       /lib/modules/4.1.6‑200.fc22.x86_64/kernel/fs/fat/vfat.ko.xz \
author:         Gordon Chaffee \
description:    VFAT filesystem support \
license:        GPL \
alias:          fs‑vfat \
depends:        fat \
intree:         Y \
vermagic:       4.1.6‑200.fc22.x86_64 SMP mod_unload  \
signer:         Fedora kernel signing key \
sig_key:        95:D8:8B:1A:62:3B:BF:DF:EF:E2:58:6B:05:ED:0A:C5:C2:88:C1:3A \
sig_hashalgo:   sha256 \
`**

The module information includes the full path to the file and information about any other names (aliases) the module might be known by. Dependencies, if any, are also listed. So, from Mod 2, you can see that the vfat module is also known as fs-vfat and depends on another module called fat. Try running modinfo with these module names.

You can use the -F or --field option to limit the output to a specific field. This option is useful for scripts.

If you don't specify a full filename, modinfo searches for a module in /lib/modules/_version_/kernel, where _version_ is your kernel release as given by uname -r. In the example in Listing 18, the file vfat.ko.xz (the vfat kernel module) is found in /lib/modules/4.1.6-200.fc22.x86_64/kernel/fs/fat. Listing 3 illustrates how you might start looking for module files on your system.


##### **Listing 3. Locating module files on your system` \
[ian@atticf22 ~]$ uname ‑r \
4.1.6‑200.fc22.x86_64 \
[ian@atticf22 ~]$ ls /lib/modules/$(uname ‑r) \
build                modules.dep          modules.softdep \
kernel               modules.dep.bin      modules.symbols \
modules.alias        modules.devname      modules.symbols.bin \
modules.alias.bin    modules.drm          source \
modules.block        modules.modesetting  updates \
modules.builtin      modules.networking   vdso \
modules.builtin.bin  modules.order \
[ian@atticf22 ~]$ ls /lib/modules/$(uname ‑r)/kernel \
arch  crypto  drivers  fs  kernel  lib  mm  net  security  sound \
`**

You can also find several plain-text files in your /lib/modules/$(uname -r) directory — including modules.dep, which lists dependencies, and modules.alias, which lists aliases. The modules.builtin file lists modules that are built in to the kernel. These include drivers needed for core functionality on most systems. If you try to use modinfo to find out about the ehci-pci driver that you might have noticed in the lsusb output, you probably won't find it because it is a built-in driver. Listing 4 illustrates this scenario.


##### **Listing 4. Finding built-in drivers` \
[ian@atticf22 ~]$ modinfo ehci‑pci \
modinfo: ERROR: Module ehci‑pci not found. \
[ian@atticf22 ~]$ grep ehci /lib/modules/4.1.6‑200.fc22.x86_64/modules.builtin \
kernel/drivers/usb/host/ehci‑hcd.ko \
kernel/drivers/usb/host/ehci‑pci.ko \
`**


### Using modprobe

As you've seen from the output of lspci, lsusb, lsmod, and modinfo, your system uses several kernel modules to drive devices. You have also seen that some of these modules have dependencies. So, loading the right modules in the right order is important. Fortunately, the modprobe command has taken over the earlier manual work of insmod and rmmod, which can each insert or remove one module from the system.

I'll use the irnet module for this example on my Ubuntu 16.04.1 LTS system. Use modinfo to see more about this module, as shown in Listing 5.


##### **Listing 5. Module information for irrnet` \
ian@ubuntu:~$ modinfo irnet \
ian@attic‑u16:~$ modinfo irnet \
filename:       /lib/modules/4.4.0‑59‑generic/kernel/net/irda/irnet/irnet.ko \
alias:          char‑major‑10‑187 \
license:        GPL \
description:    IrNET : Synchronous PPP over IrDA \
author:         Jean Tourrilhes <jt@hpl.hp.com> \
srcversion:     2883ED607A4D54A94830F37 \
depends:        irda \
intree:         Y \
vermagic:       4.4.0‑59‑generic SMP mod_unload modversions  \
ian@attic‑u16:~$ modinfo ‑F depends irda \
crc‑ccitt \
`**

As you see, this module has a dependency on irda, which has a further dependency on crc-ccitt. By using grep to search for irda or crc-ccitt in your modules.dep file, you can see the value of modularizing driver support. Each of these drivers is used by many other drivers.

If you want to manually load or unload a driver, use modprobe with the -a (or --all) option to load or insert the module and the -r option to remove or unload the module. The -n, --dry-run, or --show option shows you what will be done, without doing it. You usually use the -v option with these to see more information. Listing 6 shows what will happen if I try to load the irda module.


##### **Listing 6. Dry run loading the irnet module` \
ian@attic‑u16:~$ modprobe ‑nav irnet \
insmod /lib/modules/4.4.0‑59‑generic/kernel/lib/crc‑ccitt.ko  \
insmod /lib/modules/4.4.0‑59‑generic/kernel/net/irda/irda.ko  \
insmod /lib/modules/4.4.0‑59‑generic/kernel/net/irda/irnet/irnet.ko  \
`**

The output shows the insmod commands needed to load each required module with dependencies resolved.

The modprobe command takes into account modules that are already loaded. In Listing 7, I first load the crc-ccitt module, then do another dry run, and finally load the irnet module. Note that the actual loading or unloading requires root authority.


##### **Listing 7. Loading the irnet module` \
ian@attic‑u16:~$ sudo modprobe ‑av crc‑ccitt \
insmod /lib/modules/4.4.0‑59‑generic/kernel/lib/crc‑ccitt.ko  \
ian@attic‑u16:~$ modprobe ‑nav irnet \
insmod /lib/modules/4.4.0‑59‑generic/kernel/net/irda/irda.ko  \
insmod /lib/modules/4.4.0‑59‑generic/kernel/net/irda/irnet/irnet.ko  \
ian@attic‑u16:~$ sudo modprobe ‑av irnet \
insmod /lib/modules/4.4.0‑59‑generic/kernel/net/irda/irda.ko  \
insmod /lib/modules/4.4.0‑59‑generic/kernel/net/irda/irnet/irnet.ko  \
ian@attic‑u16:~$ lsmod | grep "ir[dn]|ccitt" \
irnet                  24576  0 \
irda                  196608  1 irnet \
crc_ccitt              16384  1 irda \
`**

You remove modules by using the -r option of modprobe. You cannot remove a module if it is being used. Listing 8 shows you some examples.


##### **Listing 8. Unloading the irnet module` \
ian@attic‑u16:~$ modprobe ‑nvr crc‑ccitt \
modprobe: FATAL: Module crc_ccitt is in use. \
ian@attic‑u16:~$ modprobe ‑nvr irnet \
rmmod irnet \
ian@attic‑u16:~$ sudo modprobe ‑vr crc‑ccitt \
modprobe: FATAL: Module crc_ccitt is in use. \
ian@attic‑u16:~$ sudo modprobe ‑vr irnet \
rmmod irnet \
rmmod irda \
rmmod crc_ccitt \
`**


### Module parameters

Some modules have parameters. For example, a device driver might need to know which IRQ or I/O port to use. Listing 9 shows module information for the nsc-ircc module, which has several such parameters.


##### **Listing 9. Modules with parameters` \
ian@attic‑u16:~$ modinfo nsc‑ircc \
filename:       /lib/modules/4.4.0‑59‑generic/kernel/drivers/net/irda/nsc‑ircc.ko \
license:        GPL \
description:    NSC IrDA Device Driver \
author:         Dag Brattli <dagb@cs.uit.no> \
srcversion:     9AA374A6DFC1C886D632DC3 \
alias:          acpi:IBM0071: \
alias:          pnp:dIBM0071 \
alias:          acpi:HWPC224: \
alias:          pnp:dHWPC224 \
alias:          acpi:NSC6001: \
alias:          pnp:dNSC6001* \
depends:        irda \
intree:         Y \
vermagic:       4.4.0‑59‑generic SMP mod_unload modversions  \
parm:           qos_mtt_bits:Minimum Turn Time (int) \
parm:           io:Base I/O addresses (array of int) \
parm:           irq:IRQ lines (array of int) \
parm:           dma:DMA channels (array of int) \
parm:           dongle_id:Type‑id of used dongle (int) \
`**

You specify module parameters on the modprobe command line when loading the module. See the man page for more details and for details on the other available modprobe options.
