# Maintainer: Jan Alexander Steffens (heftig) <heftig@archlinux.org>

pkgbase=linux-clang
pkgver=6.10.arch1
pkgrel=1
pkgdesc='Linux'
url='https://github.com/archlinux/linux'
arch=(x86_64)
license=(GPL-2.0-only)
makedepends=(
  bc
  cpio
  gettext
  libelf
  pahole
  perl
  python
  tar
  xz
  llvm
  clang
  lld
)
options=(
  !debug
  !strip
)
_srcname=linux-${pkgver%.*}
_srctag=v${pkgver%.*}-${pkgver##*.}
source=(
  https://cdn.kernel.org/pub/linux/kernel/v${pkgver%%.*}.x/${_srcname}.tar.{xz,sign}
  $url/releases/download/$_srctag/linux-$_srctag.patch.zst{,.sig}
  config  # the main kernel config file
)
validpgpkeys=(
  ABAF11C65A2970B130ABE3C479BE3E4300411886  # Linus Torvalds
  647F28654894E3BD457199BE38DBBDC86092693E  # Greg Kroah-Hartman
  83BC8889351B5DEBBB68416EB8AC08600F108CDF  # Jan Alexander Steffens (heftig)
)
# https://www.kernel.org/pub/linux/kernel/v6.x/sha256sums.asc
sha256sums=('774698422ee54c5f1e704456f37c65c06b51b4e9a8b0866f34580d86fef8e226'
            'SKIP'
            'fdb6552c43bcbc465f88c6ec2e01eb26791ac21f81687ca891ac1152503f7cad'
            'SKIP'
            '1eee90934450856b6d13dbd3edc3524e5700311ca2b8e7b9ed444d1ea94c4130')
b2sums=('bb243ea7493b9d63aa2df2050a3f1ae2b89ee84a20015239cf157e3f4f51c7ac5efedc8a51132b2d7482f9276ac418de6624831c8a3b806130d9c2d2124c539b'
        'SKIP'
        '733e830c81821a4d0fe2b4af9b80e5284d7bc625221cd2308308f24c76c3fc030ddeb400e9756bb19c75d632359e9535b8218db2421bb8ef5cdbb5e782af96bd'
        'SKIP'
        'b591064140306057ececa411931b040971cf082e42ad094f8aafbf881bb0d1afb157a6bd33cc5b10cea2aaf1e9fc0529295f91368b261f1b1b99da7792b8d84c')

export KBUILD_BUILD_HOST=archlinux
export KBUILD_BUILD_USER=$pkgbase
export KBUILD_BUILD_TIMESTAMP="Tue, 19 Jan 2038 03:14:06 +0000"

_srcpath="$BUILDDIR/$pkgbase/src/$_srcname"
_kcflags=(
  "-O2"
  "-pipe"
  "-march=native"
  "-fdebug-prefix-map=$_srcpath=."
)
_load="${MAXLOAD}"
unset MAXLOAD

if command -v ccache > /dev/null; then
  export CCACHE_NOHASHDIR=1
  export CCACHE_BASEDIR="$_srcpath"
  export PATH="/usr/lib/ccache/bin:$PATH"
fi

make() {
  command make -j "$(nproc --all)" -l"$_load" \
    LLVM=1 LLVM_IAS=1 KCFLAGS="${_kcflags[*]}" "$@"
}

prepare() {
  cd $_srcname

  echo "Setting version..."
  echo "-$pkgrel" > localversion.10-pkgrel
  echo "${pkgbase#linux}" > localversion.20-pkgname

  local src
  for src in "${source[@]}"; do
    src="${src%%::*}"
    src="${src##*/}"
    src="${src%.zst}"
    [[ $src = *.patch ]] || continue
    echo "Applying patch $src..."
    patch -Np1 < "../$src"
  done

  echo "Setting config..."
  cp ../config .config
  local _config=(
    # Enable clang thin LTO
    -d LTO_NONE
    -e HAS_LTO_CLANG
    -e LTO_CLANG_THIN
    # enable rust
    #-e RUST
    -d RUST
    # compress debuginfo
    -e DEBUG_INFO_COMPRESSED_ZSTD
    -d DEBUG_INFO_COMPRESSED_NONE
    #
    # unused modules
    # 

    # graphics
    -d DRM_GMA500
    -d DRM_I915
    -d DRM_MGAG200
    -d DRM_NOUVEAU
    -d DRM_VMWGFX
    -d DRM_XE
    # wlan
    -d WLAN_VENDOR_ZYDAS
    -d WLAN_VENDOR_SILABS
    -d WLAN_VENDOR_ATH
    -d WLAN_VENDOR_ST
    -d WLAN_VENDOR_QUANTENNA
    -d WLAN_VENDOR_RSI
    -d WLAN_VENDOR_RALINK
    -d WLAN_VENDOR_REALTEK
    -d WLAN_VENDOR_TI
    -d WLAN_VENDOR_ATH
    -d WLAN_VENDOR_BROADCOM
    -d WLAN_VENDOR_MARVELL
    -d WLAN_VENDOR_MEDIATEK
    -d WLAN_VENDOR_REALTEK
    # ethernet
    -d NET_VENDOR_AQUANTIA
    -d NET_VENDOR_BROADCOM
    -d NET_VENDOR_CAVIUM
    -d NET_VENDOR_CHELSIO
    -d NET_VENDOR_HUAWEI
    -d NET_VENDOR_INTEL
    -d NET_VENDOR_MARVELL
    -d NET_VENDOR_MELLANOX
    -d NET_VENDOR_NETRONOME
    -d NET_VENDOR_PENSANDO
    -d NET_VENDOR_QLOGIC
    -d NET_VENDOR_SOLARFLARE
    -d NET_VENDOR_ATHEROS
    -d NET_VENDOR_WANGXUN
    -d NET_VENDOR_CISCO
    -d NET_VENDOR_DEC
    -d NET_VENDOR_MICROCHIP
    -d NET_VENDOR_GOOGLE
    -d NET_VENDOR_BROCADE
    -d NET_VENDOR_FUNGIBLE
    -d NET_VENDOR_EMULEX
    -d NET_VENDOR_SOLARFLARE
    -d NET_VENDOR_AMAZON
    -d NET_VENDOR_SAMSUNG
    # Distributed Switch Architecture
    -d NET_DSA_MV88E6XXX
    -d NET_DSA_SJA1105
    -d NET_DSA_MICROCHIP_KSZ_COMMON
    -d NET_DSA_AR9331
    # file system
    -d OCFS2_FS   # Oracle cluster file system
    -d REISERFS_FS # murders wife
    # others
    -d SCSI_BNX2_ISCSI # Broadcom NetXtreme, pulls on its ethernet driver
    -d SCSI_BNX2X_FCOE
    -d INFINIBAND # computer cluster interconnect. very unlikely to be used on pc
  )
  scripts/config "${_config[@]}"
  make olddefconfig
  diff -u ../config .config || :

  make -s kernelrelease > version
  echo "Prepared $pkgbase version $(<version)"
}

build() {
  cd $_srcname
  make all
  make -C tools/bpf/bpftool vmlinux.h feature-clang-bpf-co-re=1
}

_package() {
  pkgdesc="The $pkgdesc kernel and modules"
  depends=(
    coreutils
    initramfs
    kmod
  )
  optdepends=(
    'wireless-regdb: to set the correct wireless channels of your country'
    'linux-firmware: firmware images needed for some devices'
  )
  provides=(
    KSMBD-MODULE
    VIRTUALBOX-GUEST-MODULES
    WIREGUARD-MODULE
  )
  replaces=(
    virtualbox-guest-modules-arch
    wireguard-arch
  )

  cd $_srcname
  local modulesdir="$pkgdir/usr/lib/modules/$(<version)"

  echo "Installing boot image..."
  # systemd expects to find the kernel here to allow hibernation
  # https://github.com/systemd/systemd/commit/edda44605f06a41fb86b7ab8128dcf99161d2344
  install -Dm644 "$(make -s image_name)" "$modulesdir/vmlinuz"

  # Used by mkinitcpio to name the kernel
  echo "$pkgbase" | install -Dm644 /dev/stdin "$modulesdir/pkgbase"

  echo "Installing modules..."
  ZSTD_CLEVEL=19 make INSTALL_MOD_PATH="$pkgdir/usr" INSTALL_MOD_STRIP=1 \
    DEPMOD=/doesnt/exist modules_install  # Suppress depmod

  # remove build link
  rm -rf "$modulesdir"/build
}

_package-headers() {
  pkgdesc="Headers and scripts for building modules for the $pkgdesc kernel"
  depends=(pahole)

  cd $_srcname
  local builddir="$pkgdir/usr/lib/modules/$(<version)/build"

  echo "Installing build files..."
  install -Dt "$builddir" -m644 .config Makefile Module.symvers System.map \
    localversion.* version vmlinux tools/bpf/bpftool/vmlinux.h
  install -Dt "$builddir/kernel" -m644 kernel/Makefile
  install -Dt "$builddir/arch/x86" -m644 arch/x86/Makefile
  cp -t "$builddir" -a scripts

  # required when STACK_VALIDATION is enabled
  install -Dt "$builddir/tools/objtool" tools/objtool/objtool

  # required when DEBUG_INFO_BTF_MODULES is enabled
  install -Dt "$builddir/tools/bpf/resolve_btfids" tools/bpf/resolve_btfids/resolve_btfids

  echo "Installing headers..."
  cp -t "$builddir" -a include
  cp -t "$builddir/arch/x86" -a arch/x86/include
  install -Dt "$builddir/arch/x86/kernel" -m644 arch/x86/kernel/asm-offsets.s

  install -Dt "$builddir/drivers/md" -m644 drivers/md/*.h
  install -Dt "$builddir/net/mac80211" -m644 net/mac80211/*.h

  # https://bugs.archlinux.org/task/13146
  install -Dt "$builddir/drivers/media/i2c" -m644 drivers/media/i2c/msp3400-driver.h

  # https://bugs.archlinux.org/task/20402
  install -Dt "$builddir/drivers/media/usb/dvb-usb" -m644 drivers/media/usb/dvb-usb/*.h
  install -Dt "$builddir/drivers/media/dvb-frontends" -m644 drivers/media/dvb-frontends/*.h
  install -Dt "$builddir/drivers/media/tuners" -m644 drivers/media/tuners/*.h

  # https://bugs.archlinux.org/task/71392
  install -Dt "$builddir/drivers/iio/common/hid-sensors" -m644 drivers/iio/common/hid-sensors/*.h

  echo "Installing KConfig files..."
  find . -name 'Kconfig*' -exec install -Dm644 {} "$builddir/{}" \;

  echo "Removing unneeded architectures..."
  local arch
  for arch in "$builddir"/arch/*/; do
    [[ $arch = */x86/ ]] && continue
    echo "Removing $(basename "$arch")"
    rm -rf "$arch"
  done

  echo "Removing documentation..."
  rm -rf "$builddir/Documentation"

  echo "Removing broken symlinks..."
  find -L "$builddir" -type l -printf 'Removing %P\n' -delete

  echo "Removing loose objects..."
  find "$builddir" -type f -name '*.o' -printf 'Removing %P\n' -delete

  echo "Stripping build tools..."
  local file
  while read -rd '' file; do
    case "$(file -Sib "$file")" in
      application/x-sharedlib\;*)      # Libraries (.so)
        llvm-strip $STRIP_SHARED "$file" ;;
      application/x-archive\;*)        # Libraries (.a)
        llvm-strip $STRIP_STATIC "$file" ;;
      application/x-executable\;*)     # Binaries
        llvm-strip $STRIP_BINARIES "$file" ;;
      application/x-pie-executable\;*) # Relocatable binaries
        llvm-strip $STRIP_SHARED "$file" ;;
    esac
  done < <(find "$builddir" -type f -perm -u+x ! -name vmlinux -print0)

  echo "Stripping vmlinux..."
  llvm-strip $STRIP_STATIC "$builddir/vmlinux"

  echo "Adding symlink..."
  mkdir -p "$pkgdir/usr/src"
  ln -sr "$builddir" "$pkgdir/usr/src/$pkgbase"
}

pkgname=(
  "$pkgbase"
  "$pkgbase-headers"
)
for _p in "${pkgname[@]}"; do
  eval "package_$_p() {
    $(declare -f "_package${_p#$pkgbase}")
    _package${_p#$pkgbase}
  }"
done

# vim:set ts=8 sts=2 sw=2 et:
