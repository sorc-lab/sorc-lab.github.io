[![Sorc Lab](/SorcLabLogo_White.png)](https://sorc-lab.github.io/)

# Cloning Disks Using Clonezilla
Clonezilla is an image that you can boot into at startup time that comes with a wide variety of disk tools. This
procedure details steps to clone one disk to another, assuming the source disk is smaller or the same size as the
target disk. You will be turning your PC into a disk cloning machine.

:heavy_check_mark: CloneZilla (bootable CD-ROM: CloneZilla Live 2.6.6-15 ISO)\
:heavy_check_mark: Windows 10 (free/unactivated)\
:heavy_check_mark: Source Disk: SSD (C:\\) 118 GB\
:heavy_check_mark: Target Disk: HDD (E:\\) 232 GB\

Official CloneZilla project documentation: [Clonezilla project](https://clonezilla.org/show-live-doc-content.php?topic=clonezilla-live/doc/03_Disk_to_disk_clone)

:finnadie: BACK UP IMPORTANT DATA BEFORE FOLLOWING THIS PROCEDURE!

![Disk Hardware SATA Connections](/IMG-1910.jpg)


## Boot Into Clonezilla
First, create a Clonezilla Live CD-ROM and boot into it via BIOS. This will differ a lot based on your hardware.
Creating the live CD-ROM is out of the scope of this procedure. Please follow the official docs to get. Clonezilla has
bootable USB options as well, which may be preferred.

Once Clonezilla boots to the splash screen choose `Default settings, VGA 800x600` or whatever you'd like.

![Clonezilla Splash Screen](/ocs-01-bootmenu.png)

Next, select your language and keyboard layout, similarly to how you would install a Linux distro. Select `Start_Clonezilla`,
not the shell. Then, select `device-device` from the list of options.



![Choose device-device](/ocs-05-2-device-device-clone.png)

On the next screen, choose `Expert` mode. This will be important later to get access to more options.

Next, choose `disk_to_local_disk`. Then you will be shown a screen with your actual disks displayed.

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


## Expanding Disk Volume
Even with the "Expert" option `-k1`, after booting into the new (larger) disk, the full size of disk shows only 118GB.
This is because the Windows 10 `source` disk we used has a Windows Recovery Partition.

![Windows Recovery Partition](/Screenshot 2022-03-08 133142.png)

The Windows Recovery partition is way bigger than it should be. On the `source` disk, that partition size is only 520MB.
I am not sure why this is the case, but in my experience, I have not needed to recover Windows or experienced a
corrupted OS install since the early 2000s. I always keep my data backed up to cloud storage, so if my Windows install
becomes corrupted, I would re-install and download my backup data. I would never use the recovery partition to do this,
but that is my preference. Do this at your own risk. I am happy to take this risk in order to get the extra hard drive
space.

In Windows, click the Windows icon and start typing `cmd`, or type it in the search box and it will pull up
"Command Prompt". Right-click it and select "Run as Administrator.

In command line, enter the command `diskpart` and press `ENTER`. This will drop you into the diskpart command line
application. Type in `list disk` to see what you're working with.

:finnadie: Again, be careful here. You should know which disk is the one you want to select to work with.

In this example, I only have one disk connected to the machine, which is `Disk 0`. This disk has two partitions, the
primary `C:\` and the "Windows Recovery" partition that I want to get rid of.

Enter in `select disk 0`.

```
DISKPART> select disk 0

Disk 0 is now the selected disk.
```

To see its partitions, enter `list partition`

```
Partition ###   Type        Size        Offset
-------------   -------     ------      -------
Partition 1     Primary     118 GB      1024 KB
Partition 2     Recovery    114 GB       118 GB
```

It is clear from the program output that the Recovery partition is `Partition 2`.

Type `select partition 2`, this will most likely be different in your case, so be careful and pay attention to the
readout.

Type `delete partition override` to erase the partition.

Finally, go back into Windows Disk Management program. You should now see that the sector on disk is Unallocated,
meaning it is dead space that the system cannot use. In short, the OS cannot write or read from this area of disk. We
need to Right-Click on the `C:\` drive, or whichever you have in your case, and select `Extend Volume...`

This will open up an "Extend Volume Wizard". Configure the settings however you wish, but in most cases you will want to
use the default allocation settings to just use the entire Unallocated Volume size.

![Extend Volume](/Screenshot 2022-03-12 134009.png)

After running the Wizard, you should see your volume is now utilizing the full size of your disk within a single volume.

:godmode: The Extend Volume function only works for volumes that are adjacent to the volume that is being extended. If
your partitions are all over the place, you may need to use more advanced tools.


```bash
/etc/systemd/system/network-online.targets.wants/networking.service
sudo vi /etc/systemd/system/network-online.targets.wants/networking.service
21:TimeoutStartSec=5min
sudo systemctl daemon-reload
```

---
Sorc Lab, LLC. All Rights Reserved. Anyone is permitted to copy and distribute verbatim copies of this document under
the terms of the [GNU FDL](http://www.gnu.org/licenses/fdl.html) Free Documentation License.
