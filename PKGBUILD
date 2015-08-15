# Maintainer: Zohar Malamant <dotfloat gmail com>
# Contributor: Gustavo Alvarez <sl1pkn07@gmail.com>
# Contributor: Franco Tortoriello <franco.tortoriello@gmail.com>

pkgname=doomsday-git
pkgver=20130405
pkgrel=1
pkgdesc="An advanced Doom engine that supports DOOM, Heretic and Hexen."
arch=('i686' 'x86_64')
url="http://dengine.net/"
license=('GPL2')
depends=('qt4' 'sdl_mixer' 'fmodex' 'wxpython')
makedepends=('git')
conflicts=('doomsday')
optdepends=('doom1-wad: Doom shareware',
            'heretic1-wad: Heretic shareware',
            'hexen1-wad: Hexen shareware')

_gitroot="git://deng.git.sourceforge.net/gitroot/deng/deng"
_gitname="deng"

build() {
  cd "${srcdir}"

  msg2 "NOTE: If you get 'undefined reference to' errors, remove 'doomsday'"
  msg2 "      or 'doomsday-git' packages before building this."

  msg "Force usage Python2"
    rm -rf "${srcdir}"/python
    mkdir "${srcdir}"/python
    ln -s /usr/bin/python2 "${srcdir}"/python/python
    export PATH="${srcdir}"/python:$PATH
  msg2 "Done"

  msg "Connecting to GIT server..."

    if [ -d "${srcdir}"/"${_gitname}" ] ; then
      cd "${_gitname}" && git pull
    else
      git clone "${_gitroot}"
    fi

    [ -d "${srcdir}"/"${_gitname}"-build ] && rm -fr "${srcdir}"/"${_gitname}"-build
    cp -R "${srcdir}"/"${_gitname}" "${srcdir}"/"${_gitname}"-build

  msg "GIT checkout done or server timeout"

  msg "Starting build..."
    cd "${srcdir}"/"${_gitname}"-build/doomsday

    # Fix libs path
    sed 's/lib64/lib/g' -i config_unix.pri
    
    # Dirty hack for compiling Fmodex libs
    echo -e "FMOD_DIR = \""${srcdir}"/"${_gitname}"-build/doomsday/plugins/fmod\"" > config_user.pri
    ln -s /usr/include/fmodex/* "${srcdir}"/"${_gitname}"-build/doomsday/plugins/fmod/include

    mkdir -p "${srcdir}/${_gitname}-build/build"
    cd "${srcdir}/${_gitname}-build/build"
    qmake-qt4 -r ../doomsday/doomsday.pro CONFIG+="deng_fmod release"

    make ${MAKEFLAGS}
  msg2 "Done"
}

package() {
    cd ${srcdir}/${_gitname}-build/build
    make INSTALL_ROOT=${pkgdir} install

    cat << 'EOF' > "${pkgdir}/usr/bin/launch-doomsday"
#!/usr/bin/env python2
import os, sys
os.chdir('/usr/share/doomsday/snowberry')
sys.path += '.'

import snowberry
EOF
}
