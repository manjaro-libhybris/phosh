# Maintainer. Dan Johansen <strit@manjaro.org>
# Contributor: Philip Goto <philip.goto@gmail.com>

pkgname=phosh
pkgver=0.14.0+25+g40317ebb
_commit=40317ebb3be0bb9754d0b29643586c8689d6b790
pkgrel=2
pkgdesc="A pure Wayland shell prototype for GNOME on mobile devices"
url="https://gitlab.gnome.org/World/Phosh/phosh"
license=("GPL3")
arch=('i686' 'x86_64' 'armv6h' 'armv7h' 'aarch64')
depends=('gtk3' 'libhandy>=1.1.90' 'gnome-desktop' 'gnome-session'
         'upower' 'libpulse' 'gcr' 'feedbackd' 'libnm'
         'phoc>=0.8.0' 'gnome-shell' 'callaudiod')
makedepends=('meson' 'git')
source=("git+https://gitlab.gnome.org/World/Phosh/phosh.git#commit=${_commit}"
        "git+https://gitlab.gnome.org/GNOME/libgnome-volume-control.git"
        "git+https://gitlab.gnome.org/World/Phosh/libcall-ui.git"
         https://gitlab.gnome.org/World/Phosh/phosh/-/merge_requests/938.patch
         0001-system-prompt-allow-blank-passwords.patch
         0002-fix-locale-issue.patch
         0003-fix-locale-issue-in-service-file.patch
	 "pam_phosh"
	 "sm.puri.OSK0.desktop")
sha256sums=('SKIP'
            'SKIP'
            'SKIP'
            'f99e8dacb18e7063ed86eed0903b0e090447689ca8dfbc4df691ec5776bd2bd0'
            '0c5a2dbd0512ab8eca6e667f04ba03ec1b0d2896237b10d239aca63cfc19919e'
            'b1f9083be8d1cf259a097b47c3fba4f639d597dad2a46e4234dd9c0cd2391bc2'
            '68265553dde43f02dfc91a5df09dee31d3320b114e10a46ed84ddb53f9d52489'
            'b7793f80c533e84ad8adfe8bb46c69f107575e724aa9b53b41f370baa37e4fd5'
            'f0faa73bb7793b7628b6a4ea8ab0059e13f5d46435efee2f4b8d8ac256311372')

pkgver() {
  cd $pkgname
  git describe --tags | sed 's/^v//;s/-/+/g'
}

_reverts=(
)

prepare() {
    cd $pkgname

  for _c in "${_reverts[@]}"; do
    git log --oneline -1 "${_c}"
    git revert -n "${_c}"
  done

    git submodule init
    git config --local submodule.subprojects/gvc.url "$srcdir/libgnome-volume-control"
    git submodule update
    
    git submodule init
    git config --local submodule.subprojects/gvc.url "$srcdir/libcall-ui"
    git submodule update

    local src
    for src in "${source[@]}"; do
      src="${src%%::*}"
      src="${src##*/}"
      [[ $src = *.patch ]] || continue
      echo "Applying patch $src..."
      patch -Np1 < "../$src"
    done
}

build() {
    rm -rf build
    arch-meson phosh build
    ninja -C build
}

package() {
    DESTDIR="${pkgdir}" ninja -C build install

	install -Dm644 "$srcdir"/$pkgname/data/phosh.service \
		"$pkgdir"/usr/lib/systemd/system/phosh.service
	install -Dm644 "$srcdir"/pam_phosh \
		"$pkgdir"/etc/pam.d/phosh
	install -Dm644 "$srcdir"/sm.puri.OSK0.desktop \
		"$pkgdir"/usr/share/applications/sm.puri.OSK0.desktop
}
