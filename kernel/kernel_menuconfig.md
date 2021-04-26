对于每一个配置选项，用户可以回答 "y"、 "m"或 "n"。其中 "y"表示将相应特性的支持或设备驱动程序编译进内核；

"m"表示将相应特性的支持或设备驱动程序编译成可加载模块，在需要时，可由系统或用户自行加入到内核中去；

"n"表示内核不提供相应特性或驱动程序的支持。只有 <>才能选择 M


## Compiler:Compiler:

arm-linux-gnueabihf-gcc (Linaro GCC 6.3-2017.05) 6.3.1 20170404

## Generalsetup--->
```
[ ] Compile also drivers which will not load
()  Local version - append to kernel release
自定义版本， 也就是 uname -r 可以看到的版本，可以自行修改，没多大意义。
[ ] Automatically append version information to the version strin
()  Build ID Salt
  Kernel compression mode (LZ4)  --->   //Kernel compression mode (LZMA) ，选择压缩方式。
(localhost) Default hostname
[*] Support for paging of anonymous memory (swap)
 //交换分区支持， 也就是虚拟内存支持，嵌入式不需要。

[] System V IPC
//为进程提供通信机制， 这将使系统中各进程间有交换信息与保持同步的能力。有些程序只有在选 Y 的情况下才能运行，所以不用考虑，这里一定要选
[ ] POSIX Message Queues 是 POSIX 的消息队列，它同样是一种 IPC(进程间通讯 )。建议你最好将它选上。
[] Enable process_vm_readv/writev syscalls
[ ] uselib syscall
[ ] Auditing support
  IRQ subsystem  --->
  Timers subsystem  --->
  Preemption Model (Preemptible Kernel (Low-Latency Desktop))
  CPU/Task time and stats accounting  --->
[ ] CPU isolation
  RCU Subsystem  --->
< > Kernel .config support
//将.config 配置信息保存在内核中， 选上它及它的子项使得其它用户能从 /proc/ config.gz 中得到内核的配置 ,选上，重新配置内核时可以利用已有配置
< > Enable kernel headers through /sys/kernel/kheaders.tar.xz
(17) Kernel log buffer size (16 => 64KB, 17 => 128KB)
//内核日志缓存的大小， 使用默认值即可。 12 => 4 KB ，13 => 8 KB ，14 => 16 KB 单处理器， 15 => 32 KB 多处理器， 16 => 64KB，17 => 128 KB 。
(0) CPU kernel log buffer size contribution (13 => 8 KB, 17 => 12
(13) Temporary per-CPU printk log buffer size (12 => 4KB, 13 => 8
  Scheduler features  ----
[ ] Control Group support  --- -有子项），使用默认即可，不清楚可以不选。
[ ] Namespaces support  ----
[ ] Checkpoint/restore support
[ ] Automatic process group scheduling
[ ] Boosting for CFS tasks (EXPERIMENTAL)
[ ] Enable deprecated sysfs features to support old userspace too
[ ] Kernel->user space relay support (formerly relayfs)
[] Initial RAM filesystem and RAM disk (initramfs/initrd) suppor
()    Initramfs source file(s)
[]   Support initial ramdisk/ramfs compressed using gzip
[ ]   Support initial ramdisk/ramfs compressed using bzip2
[ ]   Support initial ramdisk/ramfs compressed using LZMA
[ ]   Support initial ramdisk/ramfs compressed using XZ
[ ]   Support initial ramdisk/ramfs compressed using LZO
[ ]   Support initial ramdisk/ramfs compressed using LZ4
[]   Initrd async
  Compiler optimization level (Optimize for performance)  --->
-- Configure standard kernel features (expert users)  --->
[ ] Enable bpf() system call
[ ] Enable userfaultfd() system call
[] Enable rseq() system call
[ ]   Enabled debugging of rseq() system call
[] Embedded system
[ ] PC/104 support
  Kernel Performance Events And Counters  --->
[ ] Enable VM event counters for /proc/vmstat
[] Enable SLUB sysfs interface
[ ] Enable SLUB debugging support
[] Disable heap randomization
  Choose SLAB allocator (SLUB (Unqueued Allocator))  --->
[] Allow slab caches to be merged
[ ] SLAB freelist randomization
[ ] Harden slab freelist metadata
[] SLUB per cpu partial cache
[*] Profiling support
```

##  SystemType--->

```
 [*] MMU-based Paged Memory Management Support
          ARM system type (Allow multiple platforms to be selected)  --  (arm 占用配置，一般是厂家提供)
          Multiple platform selection  --->
      [ ] Dummy Virtual Machine
      [ ] Actions Semi SoCs  ----
      [ ] Annapurna Labs Alpine platform
      [ ] Axis Communications ARM based ARTPEC SoCs  ----
      [ ] AT91/Microchip SoCs  ----
      [ ] Broadcom SoC Support  ----
      [ ] Marvell Berlin SoCs  ----
      [ ] Conexant Digicolor SoC Support
      [ ] Samsung EXYNOS  ----
      [ ] Calxeda ECX-1000/2000 (Highbank/Midway)
      [ ] Hisilicon SoC Support
      [ ] Freescale i.MX family  ----
      [ ] Texas Instruments Keystone Devices
      [ ] MediaTek SoC Support  ----
      [ ] Amlogic Meson SoCs  ----
      [ ] Marvell PXA168/910/MMP2  ----
      [ ] Marvell Engineering Business Unit (MVEBU) SoCs  ----
      [ ] Nuvoton NPCM Architecture  ----
          TI OMAP/AM/DM/DRA Family  --->
      [ ] CSR SiRF  ----
      [ ] Qualcomm Support  ----

  [ ] ARM Ltd. RealView family  ----
      [ ] Rockchip RK2928 and RK3xxx SOCs
      [ ] Samsung S5PV210/S5PC110
      [ ] Renesas ARM SoCs  ----
      [ ] Altera SOCFPGA family  ----
      [ ] ST SPEAr Family  ----
      [ ] STMicroelectronics Consumer Electronics SOCs  ----
      [ ] STMicroelectronics STM32 family  ----
      [ ] Allwinner SoCs  ----
      [ ] Sigma Designs Tango4 (SMP87xx)
      [ ] NVIDIA Tegra  ----
      [ ] Socionext UniPhier SoCs
      [ ] ST-Ericsson U8500 Series  ----
      [ ] ARM Ltd. Versatile Express family  ----
      [ ] WonderMedia WM8850
      [ ] ZTE ZX family  ----
      [ ] Xilinx Zynq ARM Cortex A9 Platform
          *** Processor Type ***
          *** Processor Features ***
      [ ] Support for the Large Physical Address Extension
      [*] Support Thumb user binaries
      [ ] Enable ThumbEE CPU extension
      [ ] Disable I-Cache (I-bit)
      [ ] Disable branch prediction
 [*] Harden the branch predictor against aliasing attacks
      [*] Enable kuser helpers in vector page
      [ ] Enable VDSO for acceleration of some system calls
      [*] Enable the L2x0 outer cache controller
      [ ]   L2x0 performance monitor support
      [ ]   PL310 errata: Clean & Invalidate maintenance operations do
      [ ]   PL310 errata: Background Clean & Invalidate by Way operatio
      [ ]   PL310 errata: cache sync operation may be faulty
      [ ]   PL310 errata: no automatic Store Buffer drain
      [*] Make rodata strictly non-executable
      [ ] ARM errata: Stale prediction on replaced interworking branch
      [*] ARM errata: LoUIS bit field in CLIDR register is incorrect
      [ ] ARM errata: TLBIASIDIS and TLBIMVAIS operations can broadcast
      [ ] ARM errata: possible faulty MMU translations following an ASI
      [ ] ARM errata: no automatic Store Buffer drain
      [ ] ARM errata: Data cache line maintenance operation by MVA may
      [ ] ARM errata: A data cache maintenance operation which aborts,
      [ ] ARM errata: TLBI/DSB failure on Cortex-A15
      [ ] ARM errata: incorrect instructions may be executed from loop
      [ ] ARM errata: A12: some seqs of opposed cond code instrs => dea
      [ ] ARM errata: A12: sequence of VMOV to core registers might lea
      [ ] ARM errata: A12: DMB NSHST/ISHST mixed ... might cause deadlo
      [ ] ARM errata: A17: DMB ST might fail to create order between st
    [ ] ARM errata: A17: some seqs of opposed cond code instrs => dea
```



##  Bussupport--->(总线支持 )

```
      [ ] PCI support
          PCI Endpoint  --->PCI 总线支持， 主板上最长用的插槽， 最好选上， arm linux 可以不选， arm一般没有 PCI 总线。
      < > PCCard (PCMCIA/CardBus) support  ----一般笔记本有这种插槽， 笔记本选上， arm linux 不选。
```

## KernelFeatures( 内核特征 )--->

```
  [*] Symmetric Multi-Processing
  [*]   Allow booting SMP kernel on uniprocessor systems
  [*]   Support cpu topology definition
  [*]     Multi-Architected timesupport
  [*]     SMT scheduler support
  [ ] Architected timer support
  [ ] Multi-Cluster Power Management
  [ ] big.LITTLE support (Experimental)
	  Memory split (3G/1G user/kernel split)  --->
  (4) Maximum number of CPUs (2-32)
  -*- Support for hot-pluggable CPUs
  [ ] Support for the ARM Power State Coordination Interface (PSCI)
	  Timer frequency (300 Hz)  --->
  [ ] Compile the kernel in Thumb-2 mode
  [*] Runtime patch udiv/sdiv instructions into __aeabi_{u}idiv()
  -*- Use the ARM EABI to compile the kernel
  [ ]   Allow old ABI binaries to run with this kernel (EXPERIMENTA
  [ ] High Memory Support
  [*] Enable use of CPU domains to implement privileged no-access
  [*] Use PLTs to allow module memory to spill over into vmalloc ar
  (11) Maximum zone order
  [ ] Use kernel mem{cpy,set}() for {copy_to,clear}_user()
  [ ] Enable seccomp to safely compute untrusted bytecode
  [ ] Enable paravirtualization code

   [ ] Paravirtual steal time accounting
  [ ] Xen guest support on ARM
```


## Bootoptions--->

```
      -*- Flattened Device Tree support
      [*]   Support for the traditional ATAGS boot data passing
      [ ]     Provide old way to pass kernel parameters
      (0) Compressed ROM boot loader base address
      (0) Compressed ROM boot loader BSS address
      [ ] Use appended device tree blob to zImage (EXPERIMENTAL)
      ()  Default kernel command string
      [ ] Kexec system call (EXPERIMENTAL)
      [ ] Build kdump crash kernel (EXPERIMENTAL)
      -*- Auto calculation of the decompressed kernel image address
      [ ] UEFI runtime support
```

##CPUPowerManagement--->

```
        CPU Frequency scaling  --->
          CPU Idle  --->
```
##Floatingpointemulation--->

```
       *** At least one emulation must be selected ***
      [ ] VFP-format floating point maths
```

##Powermanagementoptions--->

```
      [*] Suspend to RAM and standby
      [ ]   Skip kernel's sys_sync() on suspend to RAM/standby
      [ ] Hibernation (aka 'suspend to disk')
      [ ] Opportunistic sleep
      [ ] User space wakeup sources interface
      -*- Device power management core functionality
      [*]   Power Management Debug Support
      [*]     Extra PM attributes in sysfs for low-level debugging/test
      [ ]     Test suspend/resume and wakealarm during bootup
      [ ]     Device suspend/resume watchdog
      < > Advanced Power Management Emulation
      [ ] Enable workqueue power-efficient mode by default
      [*] Energy Model for CPUs
```
##FirmwareDrivers--->

```
  [ ] ARM System Control and Management Interface (SCMI) Message Pr
      < > ARM System Control and Power Interface (SCPI) Message Protoco
      [ ] Add firmware-provided memory map to sysfs
      < > QEMU fw_cfg device support in sysfs
      [ ] Google Firmware Drivers  ----
          Tegra firmware driver  ----

```

##[*]ARMAcceleratedCryptographicAlgorithms--->

```
---------------+ |
      --- ARM Accelerated Cryptographic Algorithms

```

##[]Virtualization----
```
  --- Virtualization
```
##Generalarchitecture-dependentoptions--->

```
 < > OProfile system profiling
      [ ] Kprobes
      [*] Optimize very unlikely/likely branches
      [ ]   Static key selftest
      [*] Stack Protector buffer overflow detection
      [*]   Strong Stack Protector
          Link-Time Optimization (LTO) (EXPERIMENTAL) (None)  --->
      (8) Number of bits to use for ASLR of mmap base address
      [*] Make kernel text and rodata read-only
      [*] Set loadable kernel module data as NX and text as RO
      -*- Perform full reference count validation at the expense of spe
          GCOV-based kernel profiling  --->
```
##[*]Enableloadablemodulesupport--->

```
   --- Enable loadable module support
      [ ]   Forced module loading
      [*]   Module unloading
      [ ]     Forced module unloading
      [ ]   Module versioning support
      [ ]   Source checksum for all modules
      [ ]   Module signature verification
      [ ]   Compress modules on installation
      [ ]   Trim unused exported kernel symbols
```

##[*]Enabletheblocklayer 块设备层--->

```
Enable the block layer
			   [*]   Support for large (2TB+) block devices and files 仅在使用大于 2TB 的块设备时需要
			   [*]   Block layer SG support v4通用 SCSI 设备第四版支持。
			   [ ]   Block layer SG support v4 helper lib
			   [ ]   Block layer data integrity support块设备数据完整性支持。
			   [ ]   Zoned block device support
			   [ ]   Block device command line partition parser
			   [ ]   Enable support for block device writeback throttling
			   [ ]   Block layer debugging information in debugfs
			   [ ]   Logic for interfacing with Opal enabled SEDs
			   [ ]   Enable inline encryption support in block layer
					 Partition Types  --->
					 IO Schedulers  ---> （有子项），IO 调度器
```

## []HiddenDRMconfigsneededforGKI


## []HiddenRegmapconfigsneededforGKI

## []HiddenCRYPTOconfigsneededforGKI

## []HiddenCRYPTOconfigsneededforGKI

## []HiddenSNDconfigsneededforGKI

## []HiddenSND_SOCconfigsneededforGKI

## []HiddenGPIOconfigsneededforGKI

## []HiddenVirtualconfigsneededforGKI

## []HiddenwirelessextensionconfigsneededforGKI

## []HiddenSOCPowerManagementconfigsforGKI

## []Hiddenv4l2configsforGKI

## []GKIDummyconfigoptions

## Executablefileformats--->

## MemoryManagementoptions--->

##[*]Networkingsupport--->

```
   Networking support
				 Networking options  --->
		   [ ]   Amateur Radio support  ----
		   < >   CAN bus subsystem support  ----
		   <*>   Bluetooth subsystem support  --->
		   < >   RxRPC session sockets
		   < >   KCM sockets
		   -*-   Wireless  --->
		   < >   WiMAX Wireless Broadband support  ----
		   <*>   RF switch subsystem support  --->
		   < >   Plan 9 Resource Sharing Support (9P2000)  ----
		   < >   CAIF support  ----
		   < >   Ceph core library
		   < >   NFC subsystem support  ----
		   < >   Packet-sampling netlink channel  ----
		   < >   Inter-FE based on IETF ForCES InterFE LFB  ----
		   [ ]   Network light weight tunnels
		   < >   Network physical/parent device Netlink interface
		   < >   Generic failover module
```

##DeviceDrivers--->

```
 Generic Driver Options  --->
				   Bus devices  --->
			   < > Connector - unified userspace <-> kernelspace linker  ----
			   < > GNSS receiver support  ----
			   <*> Memory Technology Device (MTD) support  --->
			   -*- Device Tree and Open Firmware support  --->
			   < > Parallel port support  ----
			   [*] Block devices  --->
				   NVME Support  --->
				   Misc devices  --->
				   SCSI device support  --->
			   < > Serial ATA and Parallel ATA drivers (libata)  ----
			   [ ] Multiple devices driver support (RAID and LVM)  ----
			   < > Generic Target Core Mod (TCM) and ConfigFS Infrastructure  ----
			   [*] Network device support  --->
				   Input device support  --->
				   Character devices  --->
			   [ ] Trust the bootloader to initialize Linux's CRNG
				   I2C support  --->
			   [*] SPI support  --->
			   < > SPMI support  ----
			   < > HSI support  ----
			   -*- PPS support  --->
				   PTP clock support  --->
			   [*] Pin controllers  --->
			   -*- GPIO Support  --->
			   < > Dallas's 1-wire support  ----
			   [*] Adaptive Voltage Scaling class support  ----
			   [*] Board level reset or power off  --->
			   [*] Power supply class support  --->
			   < > Hardware Monitoring support  ----
			   <*> Generic Thermal sysfs driver  --->
			   [*] Watchdog Timer Support  --->
			   < > Sonics Silicon Backplane support  ----
			   < > Broadcom specific AMBA  ----
				   Multifunction device drivers  --->
< > Fairchild fusb302 Support
			   [*] Voltage and Current Regulator Support  --->
			   < > Remote Controller support  ----
			   <*> Multimedia support  --->
				   Graphics support  --->
			   <*> Sound card support  --->
				   HID support  --->
			   [*] USB support  --->
			   < > Ultra Wideband devices  ----
			   <*> MMC/SD/SDIO card support  --->
			   < > Sony MemoryStick card support  ----
			   [*] LED Support  --->
			   [ ] Accessibility support  ----
			   < > InfiniBand support  ----
			   [*] Real Time Clock  --->
			   [*] DMA Engine support  --->
				   DMABUF options  --->
			   [ ] Auxiliary Display support  ----
			   < > Userspace I/O drivers  ----
			   [ ] Virtualization drivers  ----
			   [ ] Virtio drivers  ----
				   Microsoft Hyper-V guest support  ----
			   [*] Staging drivers  --->
			   [ ] Platform support for Goldfish virtual devices  ----
			   [ ] Platform support for Chrome hardware  ----
			   [ ] Platform support for Mellanox hardware  ----
				   Common Clock Framework  --->
			   [ ] Hardware Spinlock drivers  ----
				   Clock Source drivers  --->
			   [*] Mailbox Hardware Support  --->
			   [*] IOMMU Hardware Support  --->
				   Remoteproc drivers  --->
				   Rpmsg drivers  --->
				   SOC (System On Chip) specific Drivers  --->
			   [*] Generic Dynamic Voltage and Frequency Scaling (DVFS) support  --->
			   <*> External Connector Class (extcon) support  --->
[ ] Memory Controller drivers  ----
			   <*> Industrial I/O support  --->
			   [*] Pulse-Width Modulation (PWM) Support  --->
				   IRQ chip support  ----
			   < > IndustryPack bus support  ----
			   -*- Reset Controller Support  --->
			   < > FMC support  ----
				   PHY Subsystem  --->
			   [ ] Generic powercap sysfs driver  ----
			   < > MCB support  ----
				   Performance monitor support  --->
			   [ ] Reliability, Availability and Serviceability (RAS) features  ----
				   Android  --->
			   < > DAX: direct access to differentiated memory  ----
			   -*- NVMEM Support  ----
				   HW tracing support  --->
			   < > FPGA Configuration Framework  ----
			   < > FSI support  ----
			   < > Trusted Execution Environment support
			   < > Eckelmann SIOX Support  ----
			   < > SLIMbus support  ----
[ ] Legacy DT-based Energy Model of CPUs
				   Headset device support  --->
```
##Filesystems--->

```
      < > Second extended fs support
                       < > The Extended 3 (ext3) filesystem
                       <*> The Extended 4 (ext4) filesystem
                       [*]   Use ext4 for ext2 file systems
                       [ ]   Ext4 POSIX Access Control Lists
                       [ ]   Ext4 Security Labels
                       [ ]   Ext4 Encryption
                       [ ]   EXT4 debugging support
                       [ ] JBD2 (ext4) debugging support
                       < > Reiserfs support
                       < > JFS filesystem support
                       < > XFS filesystem support
                       < > GFS2 file system support
                       < > OCFS2 file system support
                       < > Btrfs filesystem support
                       < > NILFS2 file system support
                       < > F2FS filesystem support
                       [ ] Enable filesystem export operations for block IO
                       [*] Enable POSIX file locking API
                       [*]   Enable Mandatory file locking
                       [ ] FS Encryption (Per-file encryption)
                       [ ] FS Verity (read-only file-based authenticity protection)
           [ ] FS Encryption (Per-file encryption)
                       [ ] FS Verity (read-only file-based authenticity protection)
                       [ ] Dnotify support
                       [*] Inotify support for userspace
                       [ ] Filesystem wide access notification
                       [ ] Quota support
                       < > Old Kconfig name for Kernel automounter support
                       < > Kernel automounter support (supports v3, v4 and v5)
                       < > FUSE (Filesystem in Userspace) support
                       <*> Overlay filesystem support
                       [ ]   Overlayfs: turn on redirect directory feature by default
                       [ ]   Overlayfs: follow redirects even if redirects are turned off
                       [ ]   Overlayfs: turn on inodes index feature by default
                       [ ]   Overlayfs: auto enable inode number mapping
                       [ ]   Overlayfs: turn on metadata only copy up feature by default
                       < > Incremental file system support
                           Caches  --->
                           CD-ROM/DVD Filesystems  --->
                           DOS/FAT/NT Filesystems  --->
                           Pseudo filesystems  --->
                       [*] Miscellaneous filesystems  --->
                       [*] Network File Systems  --->
         [*] Network File Systems  --->
                       -*- Native language support  --->
                       < > Distributed Lock Manager (DLM)  ----
                       [ ] UTF-8 normalization and casefolding support
````

##Securityoptions--->

```
       -*- Enable access key retention support
                       [ ]   Enable register of persistent per-UID keyrings
                       [ ]   Large payload keys
                       < >   ENCRYPTED KEYS
                       [ ]   Diffie-Hellman operations on retained keys
                       [ ] Restrict unprivileged access to the kernel syslog
                       [ ] Restrict unprivileged use of performance events
                       [ ] Enable different security models
                       [ ] Enable the securityfs filesystem
                       [ ] Harden memory copies between kernel and userspace
                       [ ] Harden common str/mem functions against buffer overflows
                       [ ] Force all usermode helper calls through a single binary
                       [ ] Trusted Execution Environment Support
                           Default security module (Unix Discretionary Access Controls)  --->
                           Kernel hardening options  --->
```

##-*-CryptographicAPI--->

```
   --- Cryptographic API
                             *** Crypto core or helper ***
                       -*-   RSA algorithm
                       < >   Diffie-Hellman algorithm
                       -*-   ECDH algorithm
                       -*-   Cryptographic algorithm manager
                       < >   Userspace cryptographic algorithm configuration
                       [*]   Disable run-time self tests
                       -*-   GF(2^128) multiplication functions
                       -*-   Null algorithms
                       < >   Parallel crypto engine
                       <*>   Software async crypto daemon
                       < >   Software async multi-buffer crypto daemon
                       < >   Authenc support
                       < >   Testing module
                             *** Authenticated Encryption with Associated Data ***
                       -*-   CCM support
                       -*-   GCM/GMAC support
                       < >   ChaCha20-Poly1305 AEAD support
                       < >   AEGIS-128 AEAD algorithm
                       < >   AEGIS-128L AEAD algorithm
                       < >   AEGIS-256 AEAD algorithm
    < >   MORUS-640 AEAD algorithm
                       < >   MORUS-1280 AEAD algorithm
                       -*-   Sequence Number IV Generator
                       < >   Encrypted Chain IV Generator
                             *** Block modes ***
                       < >   CBC support
                       < >   CFB support
                       -*-   CTR support
                       < >   CTS support
                       -*-   ECB support
                       < >   LRW support
                       < >   PCBC support
                       < >   XTS support
                       < >   Key wrapping support
                       < >   Adiantum support
                             *** Hash modes ***
                       -*-   CMAC support
                       -*-   HMAC support
                       < >   XCBC support
                       < >   VMAC support
                             *** Digest ***
                       -*-   CRC32c CRC algorithm
  < >   CRC32 CRC algorithm
                       < >   CRCT10DIF algorithm
                       -*-   GHASH digest algorithm
                       < >   Poly1305 authenticator algorithm
                       < >   MD4 digest algorithm
                       < >   MD5 digest algorithm
                       < >   Michael MIC keyed digest algorithm
                       < >   RIPEMD-128 digest algorithm
                       < >   RIPEMD-160 digest algorithm
                       < >   RIPEMD-256 digest algorithm
                       < >   RIPEMD-320 digest algorithm
                       <*>   SHA1 digest algorithm
                       -*-   SHA224 and SHA256 digest algorithm
                       < >   SHA384 and SHA512 digest algorithms
                       < >   SHA3 digest algorithm
                       < >   SM3 digest algorithm
                       < >   Tiger digest algorithms
                       < >   Whirlpool digest algorithms
                             *** Ciphers ***
                       -*-   AES cipher algorithms
                       < >   Fixed time AES cipher
    < >   Anubis cipher algorithm
                       -*-   ARC4 cipher algorithm
                       < >   Blowfish cipher algorithm
                       < >   Camellia cipher algorithms
                       < >   CAST5 (CAST-128) cipher algorithm
                       < >   CAST6 (CAST-256) cipher algorithm
                       < >   DES and Triple DES EDE cipher algorithms
                       < >   FCrypt cipher algorithm
                       < >   Khazad cipher algorithm
                       < >   Salsa20 stream cipher algorithm
                       < >   ChaCha stream cipher algorithms
                       < >   SEED cipher algorithm
                       < >   Serpent cipher algorithm
                       < >   SM4 cipher algorithm
                       < >   TEA, XTEA and XETA cipher algorithms
                       < >   Twofish cipher algorithm
                             *** Compression ***
                       < >   Deflate compression algorithm
  -*-   LZO compression algorithm
                       < >   842 compression algorithm
                       < >   LZ4 compression algorithm
                       < >   LZ4HC compression algorithm
                       < >   Zstd compression algorithm
                             *** Random Number Generation ***
                       < >   Pseudo Random Number Generation for Cryptographic modules
                       -*-   NIST SP800-90A DRBG  --->
                       -*-   Jitterentropy Non-Deterministic Random Number Generator
                       < >   User-space interface for hash algorithms
                       < >   User-space interface for symmetric key cipher algorithms
                       < >   User-space interface for random number generator algorithms
                       < >   User-space interface for AEAD cipher algorithms
                       [ ]   Hardware crypto devices  ----
                       -*-   Asymmetric (public-key cryptographic) key type  --->
                             Certificates for signature checking  --->
```

##Libraryroutines--->

```
           < > CRC-CCITT functions
                       -*- CRC16 functions
                       < > CRC calculation for the T10 Data Integrity Field
                       < > CRC ITU-T V.41 functions
                       -*- CRC32/CRC32c functions
                       < >   CRC32 perform self test on init
                             CRC32 implementation (Slice by 8 bytes)  --->
                       < > CRC64 functions
                       < > CRC4 functions
                       < > CRC7 functions
                       < > CRC32c (Castagnoli, et al) Cyclic Redundancy-Check
                       < > CRC8 function
                       [ ] PRNG perform self test on init
                       < > XZ decompression support
                       < > CORDIC algorithm
                       [ ] JEDEC DDR data
                       [ ] IRQ polling library
                       < > Test string functions
```

##Kernelhacking--->

```
       printk and dmesg options  --->
                           Compile-time checks and compiler options  --->
                       [*] Magic SysRq key
                       (0)   Enable magic SysRq key functions by default
                       [*]   Enable magic SysRq key over serial
                       -*- Kernel debugging
                           Memory Debugging  --->
                       [ ] Code coverage for fuzzing
                       [ ] Debug shared IRQ handlers
                           Debug Lockups and Hangs  --->
                       [*] Panic on Oops
                       (0) panic timeout
                       [ ] Collect scheduler debugging info
                       [ ] Collect scheduler statistics
                       [ ] Detect stack corruption on calls to schedule()
                       [ ] Enable extra timekeeping sanity checking
                       [ ] Debug preemptible kernel 
                           Lock Debugging (spinlocks, mutexes, etc...)  --->
                       [ ] Stack backtrace support
                       [ ] Warn for all uses of unseeded randomness
                       [ ] kobject debugging
                       [*] Verbose BUG() reporting (adds 70K)
   [ ] Debug linked list manipulation
                       [ ] Debug priority linked list manipulation
                       [ ] Debug SG table operations
                       [ ] Debug notifier call chains
                       [ ] Debug credential management
                           RCU Debugging  --->
                       [ ] Force round-robin CPU selection for unbound work items
                       [ ] Force extended block device numbers and spread them
                       [ ] Enable CPU hotplug state control
                       < > Notifier error injection
                       [ ] Fault-injection framework
                       [ ] Latency measuring infrastructure
                       [ ] Tracers  ----
                       [ ] Enable debugging of DMA-API usage
                       [ ] Runtime Testing  ----
                       [ ] Memtest
                       [ ] Trigger a BUG when data corruption is detected
                       [ ] Sample kernel code  ----
                       [ ] KGDB: kernel debugger  ----
     [ ] Filter access to /dev/mem
                       [ ] Export kernel pagetable layout to userspace via debugfs
                       [ ] Warn on W+X mappings at boot
                       [*] Enable stack unwinding support (EXPERIMENTAL)
                       [ ] Verbose user fault messages
                       [ ] Kernel low-level debugging functions (read help!)
                       [ ] Write the current PID to the CONTEXTIDR register
                       [ ] CoreSight Tracing Support  ----
                       [ ] Undefined behaviour sanity checker
```