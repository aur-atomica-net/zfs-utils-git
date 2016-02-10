# Maintainer: Jason R. McNeil <jason@jasonrm.net>
# Contributor: Jesus Alvarez <jeezusjr at gmail dot com>
# Contributor: Kyle Fuller <inbox at kylefuller dot co dot uk>

_spl_version=$(pacman -Q spl-git | awk '{print $2}')
_kernel_version=$(pacman -Q linux | awk '{print $2}')
_gitname="zfs"

pkgname="zfs-utils-git"
pkgver=0.6.4.r165.g3b79cef_4.1.2_2
pkgrel=1
license=('CDDL')
pkgdesc="Kernel module support files for the Zettabyte File System."
depends=("spl-git=${_spl_version}" "linux=${_kernel_version}")
makedepends=("git")
arch=("i686" "x86_64")
url="http://zfsonlinux.org/"
source=("${_gitname}::git+https://github.com/zfsonlinux/zfs.git"
        "zfs-utils.bash-completion-r1"
        "zfs-utils.initcpio.install"
        "zfs-utils.initcpio.hook")
groups=("archzfs-git")
md5sums=('SKIP'
         '9ddb0c8a94861f929d0fa741fdc49950'
         '9ef4841abb85dee611828cc7e6f5c6fe'
         '62ba32c6853c315ff231c396cf8bfdbb')
replaces=("zfs-utils")
provides=("zfs-utils")
conflicts=("zfs-utils" "zfs-utils-lts")

pkgver() {
    cd ${srcdir}/${_gitname}
    REPO_VER=$(git describe --long | sed 's/^zfs-//;s/\([^-]*-g\)/r\1/;s/-/./g')
    KERNEL_VER=$(pacman -Q linux | awk '{print $2}' | sed -r 's/-/_/g')
    echo "${REPO_VER}_${KERNEL_VER}"
}

build() {
    cd "${srcdir}/${_gitname}"
    ./autogen.sh

    ./configure --datadir=/usr/share \
                --includedir=/usr/include \
                --libdir=/usr/lib \
                --libexecdir=/usr/lib/zfs \
                --prefix=/usr \
                --sbindir=/usr/bin \
                --sysconfdir=/etc \
                --with-config=user \
                --with-mounthelperdir=/usr/bin \
                --with-udevdir=/lib/udev
    make
}

package() {
    cd "${srcdir}/${_gitname}"
    make DESTDIR="${pkgdir}" install

    # Remove uneeded files
    rm -r "${pkgdir}"/etc/init.d
    rm -r "${pkgdir}"/usr/lib/dracut

    # move module tree /lib -> /usr/lib
    cp -r "${pkgdir}"/{lib,usr}
    rm -r "${pkgdir}"/lib

    install -D -m644 "${srcdir}"/zfs-utils.initcpio.hook "${pkgdir}"/usr/lib/initcpio/hooks/zfs
    install -D -m644 "${srcdir}"/zfs-utils.initcpio.install "${pkgdir}"/usr/lib/initcpio/install/zfs
    install -D -m644 "${srcdir}"/zfs-utils.bash-completion-r1 "${pkgdir}"/usr/share/bash-completion/completions/zfs
}
