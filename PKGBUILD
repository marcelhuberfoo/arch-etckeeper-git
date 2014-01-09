# Maintainer: Christian Hesse <mail@eworm.de>
# Contributor: mathieu.clabaut <mathieu.clabaut@gmail.com>

pkgname=etckeeper-git
pkgver=1.11.r0.g214332b
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
source=("$pkgname"::'git://git.kitenet.net/etckeeper')
backup=('etc/etckeeper/etckeeper.conf')
sha256sums=('SKIP')
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
  # silence the build, especially when bzr is not installed
  sed -r -i -e 's/^(\s*)(echo|sed|rm|mkdir|cp|-\.)/\1@\2/g' \
    -e '/etckeeper-bzr/ s@\|\|@2>/dev/null ||@' Makefile 
  # setup our package manager
  sed -i -e "/^LOWLEVEL_PACKAGE_MANAGER=/ s/=.*/=pacman/" \
      -e "/^HIGHLEVEL_PACKAGE_MANAGER=/ s/=.*/=pacman/" etckeeper.conf
}

package() {
  cd "$srcdir/$pkgname"

  make DESTDIR="$pkgdir" install
  install -D -m0755 debian/cron.daily ${pkgdir}/etc/cron.daily/etckeeper
}

# vim: set ft=sh syn=sh ts=2 sw=2 et:
