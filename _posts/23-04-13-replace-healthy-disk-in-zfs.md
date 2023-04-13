---
title: Replace healty disk in ZFS
categories: [proxmox]
tags: [disk,hdd,replace,zfs]
---

# Replace healty disk in ZFS
My ZFS poll is named `pvetank`.<br><br>

Check disk status
```bash
zpool status
```
Take the disk that needs replacing offline
```bash
zpool offline pvetank ata-WDC_WD60EFRX-68L0BN1_WD-WX21DC74DPAZ
```
Check that it is now offline
```bash
zpool status
```
Shutdown and replace the disk
```bash
shutdown
```
Either 'Initialize Disk with GPT' in Proxmox GUI<br>
or in terminal:
```bash
parted /dev/new-disk
(parted)# print
(parted)# mklabel GPT
(parted)# Yes
(parted)# q
```
The command to replace the disk in the pool:<br>
`zpool replace pool /dev/disk/by-id/old /dev/disk/by-id/new`
```bash
zpool replace pvetank /dev/disk/by-id/ata-WDC_WD60EFRX-68L0BN1_WD-WX21DC74DPAZ /dev/disk/by-id/ata-WDC_WD60EFZX-68B3FN0_WD-C82P46GK
```
`zpool status` will now give you something like:
```bash
root@pve:~# zpool status
  pool: pvetank
 state: DEGRADED
status: One or more devices is currently being resilvered.  The pool will
        continue to function, possibly in a degraded state.
action: Wait for the resilver to complete.
  scan: resilver in progress since Thu Apr 13 16:58:25 2023
        5.16G scanned at 441M/s, 11.7M issued at 998K/s, 2.16T total
        0B resilvered, 0.00% done, no estimated completion time
config:

        NAME                                            STATE     READ WRITE CKSUM
        pvetank                                         DEGRADED     0     0     0
          raidz1-0                                      DEGRADED     0     0     0
            ata-WDC_WD60EFZX-68B3FN0_WD-CA1JPXZK        ONLINE       0     0     0
            replacing-1                                 DEGRADED     0     0     0
              ata-WDC_WD60EFRX-68L0BN1_WD-WX21DC74DPAZ  OFFLINE      0     0     0
              ata-WDC_WD60EFZX-68B3FN0_WD-C82P46GK      ONLINE       0     0     0
            ata-WDC_WD60EFRX-68L0BN1_WD-WX21DC7APNR3    ONLINE       0     0     0

errors: No known data errors
```