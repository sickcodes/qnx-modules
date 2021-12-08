# qnx-modules-dkms
QNX FileSystem Kernel Modules (qnx-modules-dkms)

This project installs qnx4 and qnx6 filesystem types as kernel modules.

Available on the AUR QNX Modules DKMS

```bash
git clone https://github.com/sickcodes/qnx-modules-dkms.git
cd qnx-modules-dkms

cd ./qnx4
make
cd ..


cd ./qnx6
make
cd ..

mkdir -p /usr/src/qnx4/
mkdir -p /usr/src/qnx6/

cp -r ./qnx4/* /usr/src/qnx4/
cp -r ./qnx6/* /usr/src/qnx6/

tee -a /usr/lib/modules-load.d/qnx4.conf <<< qnx4
tee -a /usr/lib/modules-load.d/qnx6.conf <<< qnx6

```

Check if your current kernel has `CONFIG_QNX4FS_FS` or `CONFIG_QNX6FS_FS`, which it most likely will not.

```bash
zcat /proc/config.gz | grep QNX
# CONFIG_QNX4FS_FS is not set
# CONFIG_QNX6FS_FS is not set
```

# Internal use-only

Update the kernel sources, if ever.

```bash
# prepare existing tree
git clone https://github.com/sickcodes/qnx-modules-dkms.git

# QNX4_MAKEFILE="$(<./qnx-modules-dkms/qnx6/Makefile)"
# QNX6_MAKEFILE="$(<./qnx-modules-dkms/qnx6/Makefile)"

rm -rf ./qnx-modules-dkms/qnx4
rm -rf ./qnx-modules-dkms/qnx6

# copy linux ./linux/fs/qnx4 and ./linux/fs/qnx6 from linux git
git clone --depth 1 https://github.com/torvalds/linux.git

cp -ra ./linux/fs/qnx4 ./qnx-modules-dkms/
cp -ra ./linux/fs/qnx6 ./qnx-modules-dkms/

rm -rf ./linux

# # use the kernel module Makefiles
# cat <<< "${QNX4_MAKEFILE}" >./qnx-modules-dkms/qnx4/Makefile
# cat <<< "${QNX6_MAKEFILE}" >./qnx-modules-dkms/qnx6/Makefile

tee -a ./qnx-modules-dkms/qnx4/dkms.conf <<< qnx4
tee -a ./qnx-modules-dkms/qnx6/dkms.conf <<< qnx6

# restore the modified OOT Makefiles
git restore ./qnx-modules-dkms/qnx4/Makefile
git restore ./qnx-modules-dkms/qnx6/Makefile

```