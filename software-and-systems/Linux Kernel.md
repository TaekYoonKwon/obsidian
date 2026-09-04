The Linux Kernel is essentially a single program which manages the collection of drivers. So for example, you may have a driver for CPU scheduler, which abstracts away all the register operations, hardware interrupts and stuff, and expose APIs, which are common to all drivers of the same class, so that the upper layer program can simply call these APIs to perform tasks on the hardware, without having to worry about the underlying hardware differences.

# What exactly is it
Linux Kernel's architecture is monolithic - kernel running as a single program. There are some implementations of kernels (not linux, mostly from 80s-90s) that tried to have "microkernel architecture", but not very successful.
## Key Features
- Resource Management - Linux can manage hardware resources like the CPU and memory allocation. The kernel can be configured to have specific policies around how the resources will be distributed and managed. 
- File System I/O - Reading and Writing to files.
- Handling System Calls - Handles requests for resources managed by kernel from programs. It would be things like spawning a new thread, scheduling a task etc.
- Device Management - The kernel has "hardware modules" which are like slots for different types of hardware devices. Each device instance can "plug themselves in", registered and available to be managed by the kernel. These modules can be found as `.ko` files in `lib/modules/<kernel-version>`. 
	- On ARM architecture, there is no BIOS enumeration where all devices are detected and configured. This means rather than dynamically detecting and configuring them, the hardware configurations are stored in [[Device Tree Blob (DTB)]]. Most manufacturers handle this - since the specific configurations within their motherboard, like how is USB and Ethernet port wired on the hardware does not change.  

# Kernel Installation
According to [[Linux Boot Sequence]], there are three extra things that the kernel requires when booting. During the installation, these three things must be installed alongside the kernel itself. 
The bootloader first reads the `/boot/extlinux/extlinux.conf`, to read which entry we should use, then find the three items below:
1. Kernel Modules - `.ko` files
2. Device Tree Blobs (DTB)
3. Initial RAM Disk - [[initrd]]
Then the kernel wakes up, with pointers to DTB, sets up initial hardware and mounts initrd. Then initrd finds the actual storage and the systemd inside it, hand it over which will initialise the rest of user workspace.

The kernel modules are specific to the kernel version. So when you install a new kernel, the module may have different APIs and protocols implemented. So one module that works for one kernel may not work with another. This is why the kernel modules are shipped with the kernel itself, at least the basic ones, so that they are guaranteed to work with the new kernel. 
The initrd's job is to locate and load these kernel modules. since they are bundled with the kernel, the initrd must also be shipped to make sure it is pointing at the correct modules. 

## APT Update
Because the kernel is essentially just a monolithic binary, and the system can have multiple kernels stored, it is entirely possible for the user to install a new kernel via APT. 
So when we call `apt install nvidia-l4t-rt-kernel`, what happens is it unpacks the kernel modules, then runs the `postinit` script for each package, which involves updating `extlinux.conf`,  regenerate `initrd` and run `depmod` to build `modules.dep`. 
> [!INFORMATION] `depmod`
> `depmod` (Dependency Modules) is a Linux command-line utility used to analyze kernel modules in `/lib/modules/$(uname -r)` and generate a `modules.dep` file and associated map files. It ensures that module dependencies are mapped so that `modprobe` can load modules in the correct order

