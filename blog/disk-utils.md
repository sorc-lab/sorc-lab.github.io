[![Sorc Lab](/SorcLabLogo_White.png)](https://sorc-lab.github.io/)

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
:heavy_check_mark: Official CloneZilla project documentation: [CloneZilla project](https://clonezilla.org/show-live-doc-content.php?topic=clonezilla-live/doc/03_Disk_to_disk_clone)

:finnadie: BACK UP IMPORTANT DATA BEFORE FOLLOWING THIS PROCEDURE!

### Boot Into CloneZilla
First, create a CloneZilla Live CD-ROM and boot into it via BIOS. This will differ a lot based on your hardware.
Creating the live CD-ROM is out of the scope of this procedure and can be referenced in the official CloneZilla docs
above.

:godmode: It is recommended to create a bootable USB drive for CloneZilla, but I prefer the CD because I already have one made.

Once CloneZilla Live boots to the splashscreen choose `Default settings, VGA 800x600` or whatever you'd like.

Next, select your language and keyboard layout, similarly to how you would install a Linux distro. Select `Start_Clonezilla`,
not the shell. Then, select `device-device` from the list of options. Finally, choose `Expert` mode.

Choose `disk_to_local_disk`.

:finnadie: WARNING: Make sure you choose the correct `source` disk here! This is the disk you want to clone.

Here is an example of what this should look like. Your screen will look different:
```
/dev/sda: ST3250318AS_ ST3250318AS_5VMPM1CY 250GB
/dev/sdb: M4-CT128M4SSD2_ M4-CT128M4SSD2_00000000114708FF31FE 128GB
```

Since we are doing a simple smaller-to-larger disk clone, we can identify our `source` disk easily as `/dev/sdb`.

:godmode: This can get more complicated if you have a stack of identical disks with the same sizes and this procedure
may require some tweaking to better identify source and target.

Next, choose your `target` disk. In this example, there is only one option, `sda`. Your setup may differ.

Clonezilla will now pull up a list of options to select from. Since we chose `Expert` we will have extra options.
The reason why we chose expert, is because we want to select the `-k1` option in a future step. For this screen, just
use the default selections and proceed.

Next, select `-sfsck` to Skip checking the source file system. This shouldn't be necessary, unless you want to.

Next, you'll see the `Clonezilla advanced extra parameters` screen. This is where you want to make sure you check `-k1`

Finally, choose what you want the machine to do when completing the clone process. This is up to you, and for this
example, we'll just choose to `-pa poweroff` to Shutdown the machine when finished.

Press `ENTER` to continue. You'll most likely get warnings that you are about to overwrite data on the `target` disk.
Say 'yes' to those warnings. Finally, it will ask if you want to clone over the boot sector, and you'll also want to say
'yes', otherwise you won't be able to boot into your cloned OS installation on the `target` disk.

You should now be seeing a screen that will show you the cloning process. When finished, the machine will shutdown.






```bash
/etc/systemd/system/network-online.targets.wants/networking.service
sudo vi /etc/systemd/system/network-online.targets.wants/networking.service
21:TimeoutStartSec=5min
sudo systemctl daemon-reload
```

---
Sorc Lab, LLC. All Rights Reserved. Anyone is permitted to copy and distribute verbatim copies of this document under
the terms of the [GNU FDL](http://www.gnu.org/licenses/fdl.html) Free Documentation License.
