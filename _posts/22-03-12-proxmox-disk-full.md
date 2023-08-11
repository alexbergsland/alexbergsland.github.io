---
title: Full disk in Proxmox
categories: [proxmox]
tags: [disk,hdd,resize]
---

# Resize vm-disk in Proxmox

Gör disken större i Proxmox GUI (stäng av först)\
(Eller med terminalen på servern:)
```bash
qm resize 100 scsi0 +4G
```
Där `100` är id på VM och disken ökas med 4GB
<br><br>

sen i VM:

```bash
sudo parted /dev/sda
print
resizepart 1 100%

# Eller resizepart 2 (beroende på partitionsnumret) och sen y på frågan
# och 100% på andra frågan, i det senaste fallet avslutade jag med
# sudo resize2fs /dev/sda2

print
quit
```

`df -h` för att kolla var det är fullt\
sen:
 ```bash
pvresize /dev/sda3
# Kolla vilken partition det gäller

sudo lvextend -l +100%FREE /dev/mapper/Quake2--vg-root
sudo resize2fs /dev/mapper/Quake2--vg-root (den här raden kanske inte behövs)
```

För piehole:

```bash
sudo parted /dev/sda
resizepart 3 100%
quit
sudo pvresize /dev/sda3
sudo lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
```