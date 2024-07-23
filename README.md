# 1. Introduction to Kernel Development

## **1.1. Linux Architecture**

- Linux is primarily divided into **User Space** & **Kernel Space**. These two components interact through a System Call Interface – which is a predefined and matured interface to Linux Kernel for Userspace applications. The below image will give you a basic understanding.

<p align="center">
  <img src="https://github.com/user-attachments/assets/e5b985fa-6ed6-4057-9453-3d3ad2544f45" alt="kernel-space-vs-user-space">
</p>

### **1.1.1. Kernel Space**

- Kernel space is where the kernel (i.e. the core of the operating system) executes (i.e. runs) and provides its services.

### **1.1.2. User Space**

- User Space is where the user applications are executed.

## **1.2. Linux Device Drivers**

- A device driver is a particular form of software application that is designed to enable interaction with hardware devices. Without the required device driver, the corresponding hardware device fails to work.
- A device driver usually communicates with the hardware by means of the communications subsystem or computer bus to which the hardware is connected. Device drivers are operating system-specific and hardware-dependent. A device driver acts as a translator between the hardware device and the programs or operating systems that use it.

## 1.3. **Linux Device Driver Types**

- In the traditional classification, there are three kinds of the device:
    - Character device
    - Block device
    - Network device
- In Linux, everything is a file. I mean Linux treats everything as a file even hardware.

### **1.3.1. Character Device**

- A char file is a hardware file that reads and writes data one character at a time, such as keyboards, mice, and serial printers. When writing to a char file, access is synchronized to prevent simultaneous writes by different users.
- Char files are used for communication and cannot be mounted.

### 1.3.2. **Block Device**

- A block file is a hardware file that reads/writes data in blocks instead of character by character. This type of file is very much useful when we want to write/read data in a bulk fashion. All our disks such are HDD, USB, and CD-ROM are block devices. This is the reason when we are formatting we consider block size.
- The writing of data is done in an asynchronous fashion, and it is CPU-intensive activity. These device files are used to store data on real hardware and can be mounted so that we can access the data we have written.

### 1.3.3. **Network Device**

- A network device is, so far as Linux’s network subsystem is concerned, an entity that sends and receives packets of data. This is normally a physical device such as an Ethernet card.
- Some network devices though are software only such as the loopback device which is used for sending data to yourself.

# 2. Presequite

## 2.1. Hardware

- Prepare the following hardware components:
    
    
    | Board | [BeagleBone Black](https://www.digikey.com/en/products/detail/beagleboard-by-seeed-studio/102110420/12719590?_ga=2.160079739.751948473.1721750791-1174106804.1719721431&_gl=1*chp10b*_gcl_au*MTQzMTI1MDcwNS4xNzE5NzIxNDMy) |
    | --- | --- |
    | Power Supply | [USB Micro for BeagleBone Green](https://www.digikey.com/en/products/detail/phihong-usa/PSA10F-050-R/4935858?_ga=2.160079739.751948473.1721750791-1174106804.1719721431&_gl=1*chp10b*_gcl_au*MTQzMTI1MDcwNS4xNzE5NzIxNDMy) |
    | Cable | [(USB to serial adapter) TTL-232R-3V3](https://www.digikey.com/en/products/detail/ftdi-future-technology-devices-international-ltd/TTL-232R-3V3/1836393) |

## 2.2. Software

- Before you start any development, you need to set an environment up. The environment dedicated to Linux development is quite simple, at least on Debian-based systems:

```bash
$ sudo apt-get update
$ sudo apt-get install gawk wget git diffstat unzip texinfo \
gcc-multilib build-essential chrpath socat libsdl1.2-dev \
xterm ncurses-dev lzop gcc-arm-linux-gnueabihf git \
bison build-essential flex swig vim
```

# **3. Source organization**

- `arch/`: The Linux kernel is a fast growing project that supports more and more architectures. That being said, the kernel wants to be as generic as possible. Architecture-specific code is separated from the rest, and falls into this directory. This directory contains processor-specific subdirectories such as `alpha/`, `arm/`, `mips/`, `blackfin/`, and so on.
- `block/`: This directory contains code for block storage devices, actually the scheduling algorithm.
- `crypto/`: This directory contains the cryptographic API and the encryption algorithms code.
- `Documentation/`: This should be your favorite directory. It contains the descriptions of APIs used for different kernel frameworks and subsystems. You should look here prior to asking any questions on forums.
- `drivers/`: This is the heaviest directory, continuously growing as device drivers get merged. It contains every device driver organized in various subdirectories.
- `fs/`: This directory contains the implementation of different filesystems that the kernel actually supports, such as *NTFS*, *FAT*, *ETX{2,3,4}*, *sysfs*, *procfs*, *NFS*, and so on.
- `include/`: This contains kernel header files.
- `init/`: This directory contains the initialization and start up code.
- `ipc/`: This contains implementation of the **Inter-Process Communication (IPC)** mechanisms, such as *message queues*, *semaphores*, and *hared memory*...
- `kernel/`: This directory contains architecture-independent portions of the base kernel.
- `lib/`: Library routines and some helper functions live here. They are generic **kernel object (kobject)** handlers, **Cyclic Redundancy Code (CRC)** computation functions, and so on.
- `mm/`: This contains memory management code.
- `net/`: This contains networking (whatever network type it is) protocols code.
- `scripts/`: This contains scripts and tools used during kernel development. There are other useful tools here.
- `security/`: This directory contains the security framework code.
- `sound/`: Audio subsystems code is here.
- `usr/`: This currently contains the initramfs implementation.

# 4. Building your kernel

## 4.1.1. Kernel configuration

- In most situations, you won’t need to create a configuration from the beginning. Each `arch/` directory contains default and useful configuration files that can serve as a starting point:

```bash
$ cd /path/to/your_kernel_dir
$ ls arch/<you_arch>/configs/
```

- For ARM-based CPUs, these configs files are located in `arch/arm/configs/`, and for an BeagleBone Black processor, the default file config is `arch/arm/configs/bb.org_defconfig`

## 4.1.2. Build procedure

- For BeagleBone Black board:

```bash
$ export WORK=/path/to/your_work_dir
$ cd ${WORK}

$ git clone https://github.com/beagleboard/linux.git
$ git checkout 5.4

$ export ARCH=arm
$ export CROSS_COMPILE=arm-linux-gnueabihf- 

$ git restore .
$ git clean . -f
$ make clean -j$(nproc)
$ make distclean -j$(nproc)

#unset LD_LIBRARY_PATH
#unset PKG_CONFIG_PATH

$ make bb.org_defconfig -j$(nproc)
$ make uImage dtbs LOADADDR=0x80008000 -j$(nproc)
$ make modules -j$(nproc)
$ make INSTALL_MOD_PATH=</where/to/install> modules_install -j$(nproc)

```

# **5. Reference**
T.B.D
