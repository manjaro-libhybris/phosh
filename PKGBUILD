# Maintainer. Dan Johansen <strit@manjaro.org>
# Contributor: Philip Goto <philip.goto@gmail.com>

pkgname=phosh
pkgver=0.4.1+5+g92c6c4a
_commit=92c6c4a9697d31a36f53d36b444501c95cb42ae2
pkgrel=1
pkgdesc="A pure Wayland shell prototype for GNOME on mobile devices"
url="https://source.puri.sm/Librem5/phosh"
license=("GPL3")
arch=('i686' 'x86_64' 'armv6h' 'armv7h' 'aarch64')
depends=('gtk3' 'libhandy' 'gnome-desktop' 'gnome-session' 'upower' 'libpulse' 'gcr' 'feedbackd' 'libnm' 'phoc')
makedepends=('meson' 'git')
source=("git+https://source.puri.sm/Librem5/phosh.git#commit=${_commit}"
        "git+https://gitlab.gnome.org/GNOME/libgnome-volume-control.git"
         0001-system-prompt-allow-blank-passwords.patch
         0002-monitor-use-a-floating-point-scale-factor.patch
         0003-keyboard-grab-manager.patch::https://source.puri.sm/Librem5/phosh/-/merge_requests/537.patch
         #0003-rotate-pp.patch
	 "pam_phosh"
	 "sm.puri.OSK0.desktop")
sha256sums=('SKIP'
            'SKIP'
            '0c5a2dbd0512ab8eca6e667f04ba03ec1b0d2896237b10d239aca63cfc19919e'
            'e645b0a44150874006455d8082580fe993a6bf38e59cad20795414ef3f5d0e2a'
            '74f12b3316616e64868f9435ac2055cce09885b51b60355afaf3dcc603715b7b'
            'b7793f80c533e84ad8adfe8bb46c69f107575e724aa9b53b41f370baa37e4fd5'
            'f0faa73bb7793b7628b6a4ea8ab0059e13f5d46435efee2f4b8d8ac256311372')

pkgver() {
  cd $pkgname
  git describe --tags | sed 's/^v//;s/-/+/g'
}

prepare() {
    cd $pkgname

    git submodule init
    git config --local submodule.subprojects/gvc.url "$srcdir/libgnome-volume-control"
    git submodule update

    local src
    for src in "${source[@]}"; do
      src="${src%%::*}"
      src="${src##*/}"
      [[ $src = *.patch ]] || continue
      echo "Applying patch $src..."
      patch -Np1 < "../$src"
    done

    sed -i -e "s|0.4.0|0.4.1|g" meson.build
}

build() {
    rm -rf build
    arch-meson phosh build
    ninja -C build
}

package() {
    DESTDIR="${pkgdir}" ninja -C build install

	install -Dm644 "$srcdir"/phosh/debian/phosh.service \
		"$pkgdir"/usr/lib/systemd/system/phosh.service
	install -Dm644 "$srcdir"/pam_phosh \
		"$pkgdir"/etc/pam.d/phosh
	install -Dm644 "$srcdir"/sm.puri.OSK0.desktop \
		"$pkgdir"/usr/share/applications/sm.puri.OSK0.desktop
}
