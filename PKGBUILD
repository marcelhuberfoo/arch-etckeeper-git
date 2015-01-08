# Maintainer: Christian Hesse <mail@eworm.de>
# Contributor: mathieu.clabaut <mathieu.clabaut@gmail.com>

pkgname=etckeeper-git
pkgver=1.17.r11.g4e8fc8c
pkgrel=1
pkgdesc="collection of tools to let /etc be stored in a git, hg or bzr repository - git checkout"
arch=('any')
url="http://kitenet.net/~joey/code/etckeeper/"
license=('GPL2')
provides=('etckeeper')
conflicts=('etckeeper')
depends=('git')
makedepends=('git')
optdepends=('mercurial: use mercurial for version control'
  'bzr: use bazaar for version control')
source=("$pkgname"::'git://git.kitenet.net/etckeeper'
  'etckeeper.service'
  'etckeeper.timer')
backup=('etc/etckeeper/etckeeper.conf')
sha256sums=('SKIP'
            'b92c15e4e2d7211ded184dccf3d4b219031eef4bc5fbe0b8d294e8c0f61195fd'
            'd69f2cdcc721b7465dd9ae41e27b08eb26252d4eed30d91bd2b800f87be3c5bf')
install="${pkgname%%-git}.install"

pkgver() {
  cd "$pkgname"
  if GITTAG="$(git describe --abbrev=0 --tags 2>/dev/null)"; then
    echo "$(sed -e "s/^${pkgname%%-git}//" -e 's/^[-_/a-zA-Z]\+//' -e 's/[-_+]/./g' <<< ${GITTAG}).r$(git rev-list --count ${GITTAG}..).g$(git log -1 --format="%h")"
  else
    echo "0.r$(git rev-list --count master).g$(git log -1 --format="%h")"
  fi
}

build() {
  cd "$srcdir/$pkgname"
  # fix python
  sed -i '/^PYTHON=/c PYTHON=python2' Makefile
  sed -i '1s/python/python2/' zypper-etckeeper.py
  # silence the build, especially when bzr is not installed
  sed -r -i -e 's/^(\s*)(echo|sed|rm|mkdir|cp|-\.)/\1@\2/g' \
    -e '/etckeeper-bzr/ s@\|\|@2>/dev/null ||@' Makefile
  # setup our package manager
  sed -r -i -e "/^LOWLEVEL_PACKAGE_MANAGER=/ s/=.*/=pacman/" \
      -e "/^HIGHLEVEL_PACKAGE_MANAGER=/ s/=.*/=pacman/" \
      -e "/AVOID_COMMIT_BEFORE_INSTALL/ s/^#//" \
      -e "/AVOID_DAILY_AUTOCOMMITS=/ s/^#//" etckeeper.conf
  # also ignore multiple generations of backup files
  sed -r -i -e "s/(pac(orig|save|new))/\1*/" update-ignore.d/01update-ignore
}

package() {
  cd "$srcdir/$pkgname"
  make DESTDIR="$pkgdir" install
  # autocommit timer
  install -D -m0755 debian/cron.daily ${pkgdir}/usr/bin/etckeeper-autocommit
  install -D -m0644 ${srcdir}/etckeeper.service ${pkgdir}/usr/lib/systemd/system/etckeeper.service
  install -D -m0644 ${srcdir}/etckeeper.timer ${pkgdir}/usr/lib/systemd/system/etckeeper.timer
}

# vim: set ft=sh syn=sh ts=2 sw=2 et:
