# hpe-vme-kvm-nvidia

    # https://enterprise-support.nvidia.com/s/article/understanding-the-iommu-linux-grub-file-configuration
    
- Goal having nvidia T4 in pt

  - host has ubuntu 22.04
  - no nvidia driver installed 
  - host is DL360pGen8
    -    BIOS has VFIO set to on
  - Grub
```
GRUB_CMDLINE_LINUX="intel_iommu=on iommu=pt initcall_blacklist=sysfb_init pcie_acs_override=downstream pcie_acs_overrid=multifunction nofb nomodeset video=vesafb:off initcall_blacklist=sysfb_init"

sudo update-grub
```

  - initramfs

```
 sudo tee /etc/modules-load.d/ipmi.conf <<< "ipmi_msghandler"     && sudo tee /etc/modprobe.d/blacklist-nouveau.conf <<< "blacklist nouveau"     && sudo tee -a /etc/modprobe.d/blacklist-nouveau.conf <<< "options nouveau modeset=0"
```

```
 lspci -nnk | grep -i nvidia
07:00.0 3D controller [0302]: NVIDIA Corporation TU104GL [Tesla T4] [10de:1eb8] (rev a1)
        Subsystem: NVIDIA Corporation TU104GL [Tesla T4] [10de:12a2]
        Kernel modules: nvidiafb, nouveau

sudo cat /etc/modprobe.d/vfio.conf
options vfio-pci ids=10de:1eb8

sudo update-initramfs -u

sudo reboot
```

# on the VM side ...
  - VM has ubuntu 22.04
  - adding the nvidia board in pt to the VM with virt manager installed on the host
```
virt-manager
```

```
ubuntu@worker-vme-gpu-1:~$ virsh list
 Id   Name           State
------------------------------
 2    vme-kvm-gpu    running
 3    lb-adc-kvm-1   running
```

  -  virsh dumpxml vme-kvm-gpu
```

    <hostdev mode='subsystem' type='pci' managed='yes'>
      <driver name='vfio'/>
      <source>
        <address domain='0x0000' bus='0x07' slot='0x00' function='0x0'/>
      </source>
      <alias name='hostdev0'/>
      <address type='pci' domain='0x0000' bus='0x07' slot='0x00' function='0x0'/>
    </hostdev>


```

# on the VM side ...



