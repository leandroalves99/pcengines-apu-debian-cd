# Debian installer CD for PC Engines APU board

This is a set of configuration files for building a Debian installation
CD optimized for the APU platform of PC Engines:  
http://www.pcengines.ch/apu.htm

This installation is only intended for an mSATA drive on the APU
board. If you plan to boot from SD card, use Voyage Linux instead:  
http://linux.voyage.hk/

The configuration files build amd64 installer for the APU platform (APU
versions 1, 2, and 3 have been tested).

The build machine MUST be of the same Debian release as the image that
you are building: a Jessie host can produce a Jessie ISO, and a Stretch
host can produce a Stretch ISO.

Before using these scripts, have a look at the files in profiles/, and
you may want to change some options.

There are two profiles defined in profiles/ directory: "apu64" and "manual".

## Profiles
### *apu64* profile

This profile is designed to automate as much as possible.

The default and only user is set to "**root**" and the password is "**debian**".  
The default language is English (US) and the keyboard layout is entered manually.  
This can be changed in "profiles/apu64.preseed".

The SSH daemon has `PermitRootLogin yes` enable, which allows the root to login via ssh.

The disk layout does not allocate a swap partition (the APU board has 2
or 4GB RAM). If you need swap, you can add a swapfile in root
partition. The system is optimized for minimal swap usage:
vm.swappiness=1 in /etc/sysctl.d/pcengines_apu.conf

The profile adds an hourly cronjob
(/etc/cron.hourly/pcengines_apu_fstrim) which performs fstrim command on
root and boot partitions once a day.

### *manual* profile

This profile disables the automation, and lets you choose the region,
the debian mirror, the hostname and password, and it allows you to
choose the disk partitioning that you need. You can also configure
software RAID if you have multiple physical disks.

The manual profile does not install the fstrim cronjob.


## Installation
The installation ISO image is about 300MB in size, and it's a network installation.

To create the CD image on the build machine:
1. Install prerequisites:
```
apt-get update
apt-get install -y simple-cdd git xorriso lsb-release
```

2. Clone this repository and cd to repository folder:
```
git clone https://github.com/ssinyagin/pcengines-apu-debian-cd.git
cd pcengines-apu-debian-cd
```  
  - Add executable rights to .postinst file:  `chmod +x profiles/apu64.postinst`

3. Build CD image:
```
./build apu64
```
When step 3. is finished, the CD image is available at images/.


## Notes

The kernel boots with "elevator=deadline", which optimizes the I/O
performance for SSD drives.

See also SSD optimization tips:
https://wiki.freeswitch.org/wiki/SSD_Tuning_for_Linux
https://wiki.debian.org/SSDOptimization

If you need a live or rescue CD, you can get a Live CD from Voyage
Linux. It works fine, the only minor issue is that it switches the
serial console to 9600 baud: http://linux.voyage.hk/.
