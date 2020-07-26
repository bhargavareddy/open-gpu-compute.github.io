# How to install AMD Driver on Azure

```bash
$ sudo apt update
$ sudo apt install -y libnuma-dev
$ wget -q -O - http://repo.radeon.com/rocm/apt/debian/rocm.gpg.key | sudo apt-key add -
$ echo 'deb [arch=amd64] http://repo.radeon.com/rocm/apt/debian/ xenial main' | sudo tee /etc/apt/sources.list.d/rocm.list
$ sudo apt update
$ sudo apt install -y rocm-dkms rocm-libs hipcub miopen-hip rccl
```

Output:
```
update-alternatives: using /usr/bin/g++ to provide /usr/bin/c++ (c++) in auto mode
Setting up hip-rocclr (3.5.20214.5355-rocm-rel-3.5-30-a2917cd) ...
Setting up build-essential (12.4ubuntu1) ...
Setting up miopen-hip (2.4.0.8035-rocm-rel-3.5-30-bd4a330) ...
Setting up gcc-multilib (4:7.4.0-1ubuntu2.3) ...
Setting up rocm-dev (3.5.0-30) ...
Setting up rock-dkms (1:3.5-30) ...
Loading new amdgpu-3.5-30 DKMS files...
Building for 5.3.0-1032-azure
Building for architecture x86_64
Building initial module for 5.3.0-1032-azure
Can't load /var/lib/shim-signed/mok/.rnd into RNG
140092345053632:error:2406F079:random number generator:RAND_load_file:Cannot open file:../crypto/rand/randfile.c:88:Filename=/var/lib/shim-signed/mok/.rnd
Generating a RSA private key
..................................................+++++
.......+++++
writing new private key to '/var/lib/shim-signed/mok/MOK.priv'
-----
EFI variables are not supported on this system
/sys/firmware/efi/efivars not found, aborting.
Done.
Forcing installation of amdgpu
amdgpu:
Running module version sanity check.
 - Original module
 - Installation
   - Installing to /lib/modules/5.3.0-1032-azure/updates/dkms/
amdttm.ko:
Running module version sanity check.
 - Original module
 - Installation
   - Installing to /lib/modules/5.3.0-1032-azure/updates/dkms/
amdkcl.ko:
Running module version sanity check.
 - Original module
 - Installation
   - Installing to /lib/modules/5.3.0-1032-azure/updates/dkms/
amd-sched.ko:
Running module version sanity check.
 - Original module
 - Installation
   - Installing to /lib/modules/5.3.0-1032-azure/updates/dkms/
depmod.......
Backing up initrd.img-5.3.0-1032-azure to /boot/initrd.img-5.3.0-1032-azure.old-dkms
Making new initrd.img-5.3.0-1032-azure
(If next boot fails, revert to initrd.img-5.3.0-1032-azure.old-dkms image)
update-initramfs.......
DKMS: install completed.
Setting up rocsparse (1.12.10.799-rocm-rel-3.5-30-108c40b) ...
Setting up hipsparse (1.6.5.282-rocm-rel-3.5-30-9291ddc) ...
Setting up g++-multilib (4:7.4.0-1ubuntu2.3) ...
Setting up rocm-dkms (3.5.0-30) ...
Setting up rccl (2.10.0-112-g250d820-rocm-rel-3.5-30) ...
Setting up rocprim (2.10.1.1038-rocm-rel-3.5-30-8e6861f) ...
Setting up rocfft (1.0.3.839-rocm-rel-3.5-30-da61945) ...
Setting up hipcub (2.10.1.142-rocm-rel-3.5-30-419b5fb) ...
Setting up rocthrust (2.10.1.466-rocm-rel-3.5-30-f09291d) ...
Setting up rocalution (1.9.1.491-rocm-rel-3.5-30-efe39a5) ...
Setting up rocm-libs (3.5.0-30) ...
Processing triggers for man-db (2.8.3-2ubuntu0.1) ...
Processing triggers for libc-bin (2.27-3ubuntu1.2) ..
```

```bash
$ groups
azureuser adm dialout cdrom floppy sudo audio dip video plugdev lxd netdev
$ /opt/rocm/bin/rocminfo
```

Output:
```
ROCk module is NOT loaded, possibly no GPU devices                                                    
Unable to open /dev/kfd read-write: No such file or directory
azureuser is member of video group
hsa api call failure at: /src/rocminfo/rocminfo.cc:1142
Call returned HSA_STATUS_ERROR_OUT_OF_RESOURCES: The runtime failed to allocate the necessary resources. 
This error may also occur when the core runtime library needs to spawn threads or create internal OS-specific events.
```

Found an [open issue](https://github.com/RadeonOpenCompute/ROCm/issues/923) with the same problem. Looks like AMD GPU is 'UNCLAIMED'


```
$ sudo lshw -C display
```

```
  *-display
       description: VGA compatible controller
       product: Hyper-V virtual VGA
       vendor: Microsoft Corporation
       physical id: 8
       bus info: pci@0000:00:08.0
       version: 00
       width: 32 bits
       clock: 33MHz
       capabilities: vga_controller bus_master rom
       configuration: driver=hyperv_fb latency=0
       resources: irq:11 memory:f8000000-fbffffff memory:c0000-dffff
  *-display UNCLAIMED
       description: VGA compatible controller
       product: Vega 10 [Radeon Instinct MI25 MxGPU]
       vendor: Advanced Micro Devices, Inc. [AMD/ATI]
       physical id: 1
       bus info: pci@c9e3:00:00.0
       version: 00
       width: 64 bits
       clock: 33MHz
       capabilities: pciexpress msi msix vga_controller cap_list
       configuration: latency=0
       resources: iomemory:f0-ef iomemory:f0-ef memory:fe0000000-fefffffff memory:ff0000000-ff01fffff memory:40880000-408fffff
```
