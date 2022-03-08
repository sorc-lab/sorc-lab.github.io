[![Sorc Lab](/9bc734d15e601f537f61f4d135ac38ef.png)](https://sorc-lab.github.io/)

# Disk Utils
Procedures covering common use cases, such as cloning an existing OS install from a small drive to a larger drive,
zeroing out data on disks before recycling them, and creating bootable SD card images for Respberry Pi devices.

- Cloning Disks
- Zeroing Disks
- Creating SD Card Images

## Cloning Disks
:heavy_check_mark: Software: CloneZilla (bootable CD-ROM: CloneZilla Live 2.6.6-15 ISO)\
:heavy_check_mark: OS: Windows 10 (free version)\
:heavy_check_mark: Source Disk: SSD (C:\\) 118 GB\
:heavy_check_mark: Target Disk: HDD (E:\\) 232 GB\
:heavy_check_mark: Official CloneZilla project documentation: ![CloneZilla project](https://clonezilla.org/show-live-doc-content.php?topic=clonezilla-live/doc/03_Disk_to_disk_clone)

:finnadie: BACK UP IMPORTANT DATA BEFORE FOLLOWING THIS PROCEDURE!









```bash
/etc/systemd/system/network-online.targets.wants/networking.service
sudo vi /etc/systemd/system/network-online.targets.wants/networking.service
21:TimeoutStartSec=5min
sudo systemctl daemon-reload
```

---
Sorc Lab, LLC. All Rights Reserved. Anyone is permitted to copy and distribute verbatim copies of this document under
the terms of the ![GNU FDL](http://www.gnu.org/licenses/fdl.html) Free Documentation License.
