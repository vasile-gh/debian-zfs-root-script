# debian-zfs-root-script
Script to bootstrap a zfs root based debian from a basic debian install (which will afterwards be used as rescue install)

## Prerequisites:
* a basic Debian (at least v11, `bullseye`) install
* the disk must be partitioned with either MBR or GPT partition types
* **if** the disk is **MBR**, then
  * you may have **at most** two **primary** partitions (zfs root debian requires two primary partitions to install: one for the boot zpool and the other for the system zpool)
  * **no** extended partitions and
  * the Debian root **must** be on the last one
* **if** the disk is **GPT**, then
  * the Debian root **must** be on the last partition
* you must have at least 4GB free space **after** the last partition
* your system **must** be configured to have passwordless ssh into root and the `authorized_keys` file must be in the .ssh subfolder of the root home folder 

## How To
1. Copy the three scripts in your root home folder.
2. Change first section of `zroot-install` to set up the parameters for your zfs root system.
3. Run `zroot-install`
4. Reboot
5. You may be unable to ssh into the new system after the first run (you will certainly be able to log on at the console). In this case just reboot a second time.
7. Enjoy.
8. In case you mess up later, you will be able to revert your zfs system to its pristine state by rolling back to the snapshots handily created by this script at installation time

## What you will end up with:
* a dual boot system with
  * a rescue system (the original install)
  * your zfs root debian install
* the zfs root system will have a dedicated boot pool (grub only supports certain zfs pool flags so you want to keep it separate from the main system
* the zfs boot system will be passwordless ssh enabled with the same ssh keys as the original system
* it will be a standard basic debian without any desktop environment - up to you to use tasksel to install what you want.
* the zfs root system will occupy the entire (formerly) empty space after the rescue root partition.

## Rescue HowTo

if you need to boot into rescue to fix something, you have two handy scripts to mount / umount the zfs root so you can chroot into it:
* `zmount` will mount the zfs system and you can just `chroot /tmp/target` into it to sort out
* `zumount` will umount and export the zfs root system so you can reboot into it
**NB.** Don't forget to `zumount` or your zfs root system won't boot properly. To fix that if you forgot, you just reboot into the rescue and `zmount` then `zumount`

## Tips
* if you run `update-grub` from non-chrooted rescue you will lose the zfs root boot entry. To fix that you need to chroot into zfs and run `update-grub` from there.
* ... more might come
