[![Sorc Lab](/SorcLabLogo_White.png)](https://sorclab.com/)

# DD Copy
Most Linux distros come with a program called `dd`. This tool can be used to mount an SD card and write binary copies of
images to the SD card, like a Raspberry Pi OS.

:sparkles: In Linux command line, execute `man dd` to see official documentation.

## Setup
I've gutted an old Gateway laptop to serve as a dedicated SD card image burner, running Ubuntu (Linux). The sole purpose
of this machine is to burn SD card images for Raspberry Pis to boot.

:crystal_ball: See the SD card port in the bottom left.


## Reduce Startup Timeout
Ubuntu at some point introduced a network-related timeout when the system boots without an internet connection. By
default, the configuration is set to retry the internet connection for five minutes, which is ridiculous if you are
intentionally disconnected from the net.

Change `TimeoutStartSec` setting in `/etc/systemd/system/network-online.targets.wants/networking.service`

- `sudo vi /etc/systemd/system/network-online.targets.wants/networking.service`
- Change `21:TimeoutStartSec=5min` from `5min` to `1sec`
- Run `sudo systemctl daemon-reload` for changes to take effect.


## Burning SD Card Image
DD Copy will run a bit-by-bit copy of any `.img` file to the SD card. This is much simpler and with less
features than [using Clonezilla to write images to disk](https://sorc-lab.github.io/blog/dd-copy.html).


### Locate the SD Card Volume
```
TODO: Get rid off all the df -h stuff here. fdisk is far better and needs to be used for both mounting usb sticks and
        id'ing SD cards
```

Before inserting the SD card, run `df -h` to see what volumes are currently mounted. Your output will differ.
```
Filesystem                              Size    Used    Avail   Use%    Mounted On
udev                                    1.9G    0       1.9G    0%      /dev
tmpfs                                   389M    5.7M    383M    2%      /run
/dev/mapper/SORC--DISC--UTIL--vg-root   454G    1.5G    430G    1%      /
tmpfs                                   1.9G    0       1.9G    0%      /dev/shm
tmpfs                                   5.0M    0       5.0M    0%      /run/lock
tmpfs                                   1.9G    0       1.9G    0%      /sys/fs/cgroup
/dev/sda1                               472M    57M     391M    13%     /boot
tmpfs                                   389M    0       389M    0%      /run/user/1000
```
:crystal_ball: See that `/dev/mapper/SORC...` is our main disk and `/dev/sda1` is our boot sector.

Insert the SD card and run `df -h` again. If you're able to see the new volume in the list, then that's your target.
However, when you insert the SD card the terminal may output something similar to the following:
```
[   40.566936] sd 6:0:0:0: [sdb] No Caching mode page found
[   40.566973] sd 6:0:0:0: [sdb] Assuming drive cache: write through
```
:sparkles: It is strange the SD card does not show up in `df -h`, but it is detected as `sdb`

A better way to find out which volume the SD card is allocated to, is to run `sudo fdisk -l | more`
This will output all volumes and partitions regardless if they are mounted or not.

Run the `fdisk` command before and after inserting the SD card to clearly determine which volume is being used for it.


### Unmount the SD Card Device
Normally, if the SD card shows up in `df -h`, that means it is mounted. If you see it in the list, go ahead and unmount
it now so you can write to it.

:skull: Proceed with caution and backup any important data before doing this.

Execute: `umount /dev/sdb`

:sparkles: If the SD card did not show up in `df -h`, skip this step.


### Get Raw Device Name of SD Card
If you were able to detect your SD card device from `df -h`, the device may have been labeled something like
`/dev/sdb1`, or something with a numeric value. The raw device name, in this case, would be `/dev/sdb`. This is the
device name you want to use when running DD Copy.

:skull: Get the correct device name, or you may start writing bits to something like your main hard drive!

In our example, we already have the raw device name `/dev/sdb` and it is already unmounted. Linux never mounted it when
we inserted the SD card. Again, use `fdisk` command to get a more detailed list of volumes. **The raw device name will
be something like `sda` or `sdb`, not something like `sdc1` etc.**


### Download Desired OS (if connected to the internet)
```
$ cd ~/
$ wget -S https://downloads.raspberrypi.org/raspbian_latest
```

### Import Desired OS via USB Mount (if disconnected from the internet)
Download desired OS via internet-connected machine. (see above example for downloading via `wget` for Linux)

If using a GUI based OS, like Windows or MacOS, drag and drop the OS `raspbian_latest` image onto a USB stick.

If using a Linux terminal, follow the procedure for mounting and writing the image to the USB stick.


### Mount OS Image to USB (Linux)
TODO: Follow docs from https://github.com/sorc-lab/gitlab-lfs#optional-copy-files-to-external-drive-usb
TODO: Update the `df-h` section to instead use `sudo fdisk -l` for a much better way of detecting the SD card.







---
Sorc Lab, LLC. All Rights Reserved. Anyone is permitted to copy and distribute verbatim copies of this document under
the terms of the [GNU FDL](http://www.gnu.org/licenses/fdl.html) Free Documentation License.
