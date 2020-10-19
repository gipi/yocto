# Yocto

This repo contains the [kas](kas.readthedocs.io) configuration to build a yocto
image for beaglebone black used for backups configured to use
[Mender](https://docs.mender.io) for the update process.

If your system is not supported out-of-the-box for yocto builds you can use the
docker in this repo:

```
$ cd docker && docker build -t gp/yocto . && cd -
$ docker run -it -v $PWD:/app gp/yocto
```

``MENDER_DATA_PART_SIZE_MB`` is the size of the **fixed** data partition,
``MENDER_STORAGE_TOTAL_SIZE_MB`` is the size of the sd card; the A/B partitions
have as size the difference divided by two of these values.

## Update

Once the build is finished you can enable a web server in the build directory
and call manually the mender command for updating: below a log of the process

```
(build) $ cd build/tmp/deploy/images/beaglebone-yocto && python -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
(bbb) $ mender install http://192.168.1.14:8000/core-image-minimal-beaglebone-yocto-grub-20201017151301.mender
INFO[0000] Loaded configuration file: /var/lib/mender/mender.conf  module=config
INFO[0000] Loaded configuration file: /etc/mender/mender.conf  module=config
INFO[0000] Mender running on partition: /dev/mmcblk0p2   module=cli
INFO[0000] Performing remote update from: [http://192.168.1.14:8000/core-image-minimal-beaglebone-yocto-grub-20201017151301.mender].  module=standalone
Installing Artifact of size 60117504...
INFO[0000] no public key was provided for authenticating the artifact  module=installer
INFO[0000] opening device /dev/mmcblk0p3 for writing     module=block_device
INFO[0000] native sector size of block device /dev/mmcblk0p3 is 512, we will write in chunks of 1048576  module=block_device
................................   1% 1024 KiB
................................   3% 2048 KiB
................................   5% 3072 KiB
 <...snip...>
...............................  97% 57344 KiB
................................  99% 58368 KiB
..........INFO[0075] 0 bytes remaining to be written               module=limited_writer
INFO[0075] The optimized block-device writer wrote a total of 217 frames, where 178 frames did need to be rewritten  module=block_device
INFO[0075] Wrote 226492416/226492416 bytes to the inactive partition  module=dual_rootfs_device
                       100% 58708 KiB
INFO[0075] Enabling partition with new image installed to be a boot candidate: 3  module=dual_rootfs_device
Use -commit to update, or -rollback to roll back the update.
At least one payload requested a reboot of the device it updated.
```

After completed the process you can reboot

```
# reboot
 ...
Welcome to GRUB!
lock: OK
lock: OK
Booting new update...
 ...
Poky (Yocto Project Reference Distro) 3.0.4 beaglebone-yocto ttyS0

beaglebone-yocto login: root
#
```

Take in mind that the old name for the artifact is shown until the ``commit`` is
executed

```
root@beaglebone-yocto:~# mender show-artifact
release-no-nfs-w-package-management
root@beaglebone-yocto:~# mender commit
INFO[0000] Loaded configuration file: /var/lib/mender/mender.conf 
INFO[0000] Loaded configuration file: /etc/mender/mender.conf 
INFO[0000] Mender running on partition: /dev/mmcblk0p3  
INFO[0000] Update Module path "/usr/share/mender/modules/v3" could not be opened (open /usr/share/mender/modules/v3: no such file or directory). Update modules will not be available 
Committing Artifact...
INFO[0000] Committing update                            
root@beaglebone-yocto:~# mender show-artifact
release-no-nfs-w-package-management-kas
```

## Extra

If you want to boot directly from sd card the very rough way is to destroy the
eMMC overwriting the boot sector:

```
# dd if=/dev/zero of=/dev/mmcblk1 bs=1M count=1
```

## Links

 - [Mender's documentation about manual updating](https://docs.mender.io/artifact-creation/standalone-deployment)
