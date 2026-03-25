# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2022, 2023, 2024, 2025  Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.  If not, see <https://www.gnu.org/licenses/>.

# Maintainer:
#   Truocolo
#     <truocolo@aol.com>
#     <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
#   Pellegrino Prevete (dvorak)
#     <pellegrinoprevete@gmail.com>
#     <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
#   David Runge
#     <dvzrv@archlinux.org>
#   Jesus Alonso
#     <doragasu at hotmail dot com>

# NOTE: As I want these packages for Genesis/Megadrive development, they do
# only support the m68000 CPU. If you want to support other m68k variants,
# either modify _target_cpu to suit your needs, or go wild, remove the
# --with-cpu switches and change --disable-multilib with --enable-multilib.
# Be warned that multilib packages will take a lot more time to build, and
# will also require more disk space.

_evmfs_available="$(
  command \
    -v \
    "evmfs" || \
    true)"
if [[ ! -v "_evmfs" ]]; then
  if [[ "${_evmfs_available}" != "" ]]; then
    _evmfs="true"
  elif [[ "${_evmfs_available}" == "" ]]; then
    _evmfs="false"
  fi
fi
_target=m68k-elf
_target_cpu=m68000
_pkg=newlib
pkgbase="${_target}-${_pkg}"
pkgname=(
  "${pkgbase}"
)
# Latest version 4.4.0.20231231 does not build with GCC 14.1, so stay in previous release
pkgver=4.5.0
_suffix=.20241231
pkgrel=7
pkgdesc="C library for bare metal systems (${_target})."
arch=(
  # Why was it reported 'any'?
  "aarch64"
  "arm"
  "armv8l"
  "armv7l"
  "mips"
  "i686"
  "pentium4"
  "powerpc"
  "x86_64"
)
url="https://sourceware.org/${_pkg}/"
license=(
  'BSD'
)
groups=(
  "devel"
)
depends=(
  "${_target}-binutils"
)
makedepends=(
  "${_target}-gcc-bootstrap>=4.3.0"
)
options=(
  '!makeflags'
  '!strip'
  'staticlibs'
  '!libtool'
)
PKGEXT="pkg.tar.xz"
_tarname="${_pkg}-${pkgver}"
source=(
  "ftp://sourceware.org/pub/${_pkg}/${_tarname}${_suffix}.tar.gz"
)
_sum="33f12605e0054965996c25c1382b3e463b0af91799001f5bb8c0630f2ec8c852"
_sig_sum="b98164ce5cd3754859daac579336106063affefb0618dcaf52be28192f9828b3"
sha256sums=(
  "${_sum}"
)
sha512sums=(
  'd391ea3ac68ddb722909ef790f81ba4d6c35d9b2e0fcdb029f91a6c47db9ee94a686a2bdff211fb84025e1a317e257acfa59abda3fd2bc6609966798e1c604dc'
)

prepare() {
  mkdir \
    -p \
    "${srcdir}/${_pkg}-build"
}

build() {
  local \
    _cflags=() \
    _configure_opts=()
  _cflags+=(
    -Os
    -g
    -ffunction-sections
    -fdata-sections
    -fomit-frame-pointer
    -ffast-math
    -Wno-implicit-function-declaration
    -Wno-implicit-int
  )
  _configure_opts+=(
    --target="${_target}"
    --prefix="/usr"
    --disable-"${_pkg}"-supplied-syscalls
    --disable-multilib
    --with-cpu="${_target_cpu}"
    --disable-nls
  )
  cd \
    "${srcdir}/${_pkg}-build"
  # Should remove -Wno-implicit-function-declaration
  # and -Wno-implicit-int when newlib fixes the build
  export CFLAGS_FOR_TARGET="${_cflags[*]}"
  "../${_tarname}${_suffix}/configure" \
    "${_configure_opts[@]}"
  make
}

package() {
  cd \
    "${srcdir}/${_pkg}-build"
  make \
    DESTDIR="${pkgdir}" \
    install
  # usr/share/info/porting.info.gz
  # conflicts with newlib installs
  # for other architectures
  rm \
    -r \
    "${pkgdir}/usr/share"
}
