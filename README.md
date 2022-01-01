# qnx-modules-dkms
QNX FileSystem Kernel Modules (qnx-modules-dkms)

This project installs qnx4 and qnx6 filesystem types as kernel modules.

Available on the AUR QNX Modules DKMS: [https://aur.archlinux.org/packages/qnx-modules-dkms/](https://aur.archlinux.org/packages/qnx-modules-dkms/)

AUR PKGBUILD: [https://github.com/sickcodes/aur/tree/master/qnx-modules-dkms](https://github.com/sickcodes/aur/tree/master/qnx-modules-dkms)

```PKGBUILD
# Maintainer: Sick Codes <info at sick dot codes>

pkgname=qnx-modules-dkms
_pkgname=${pkgname%-*}
pkgver=r9.11df204
arch="$(uname -r)"
url='https://github.com/sickcodes/qnx-modules'
pkgrel=1
pkgdesc='QNX4 and QNX6 FileSystem Types as Kernel Modules (dkms). BlackBerry qnxfs compatible.'
arch=('x86_64' 'aarch64' 'i386')
license=('GPL3')
provides=("${_pkgname}")
depends=('dkms')
makedepends=('git')
source=("git+${url}.git"#commit=11df204f32640c7d5ea402f3afd828a085504a3a)
sha256sums=('SKIP')

pkgver() {
  cd "${_pkgname}"
  printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

# prepare() {
#   cd "${srcdir}/${_pkgname}"
# }

package() {
  
  cd "${srcdir}/${_pkgname}"

  install -Ddm755 "${pkgdir}/usr/src/qnx4-${pkgver}/"
  install -Ddm755 "${pkgdir}/usr/src/qnx6-${pkgver}/"

  cp -dr --preserve=ownership "${srcdir}/${_pkgname}/qnx4/"* "${pkgdir}/usr/src/qnx4-${pkgver}/"
  cp -dr --preserve=ownership "${srcdir}/${_pkgname}/qnx6/"* "${pkgdir}/usr/src/qnx6-${pkgver}/"

  cd "${srcdir}/${_pkgname}/qnx4"
  make
  make DESTDIR="${pkgdir}/usr/src/qnx4-${pkgver}/" install

  cd "${srcdir}/${_pkgname}/qnx6"
  make
  make DESTDIR="${pkgdir}/usr/src/qnx6-${pkgver}/" install

  # Set name and version
  sed -e "s/@_PKGBASE@/qnx4/" \
      -e "s/@PKGVER@/${pkgver}/" \
      -i "${pkgdir}/usr/src/qnx4-${pkgver}/dkms.conf"

  sed -e "s/@_PKGBASE@/qnx6/" \
      -e "s/@PKGVER@/${pkgver}/" \
         "${pkgdir}/usr/src/qnx6-${pkgver}/dkms.conf"

  echo '** To activate qnx4 and/or qnx6 **
# $ sudo modprobe qnx4
# $ sudo modprobe qnx6'
  
}

# sudo modprobe qnx4 qnx6
```

For non-Arch users:

```bash
cd qnx6
make
sudo insmod qnx6.ko
cd ..
cd qnx6
make
sudo insmod qnx6.ko

sudo modprobe qnx4 qnx6
```

This is for Kernel configs without QNX4FS or QNX6 enabled.
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
