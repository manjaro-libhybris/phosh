# Maintainer. Dan Johansen <strit@manjaro.org>
# Contributor: Philip Goto <philip.goto@gmail.com>

pkgname=phosh
pkgver=0.5.0
_commit=b5531cb4eeaf0f7cf32ea87ddcbcce8f037a5805
pkgrel=2
pkgdesc="A pure Wayland shell prototype for GNOME on mobile devices"
url="https://source.puri.sm/Librem5/phosh"
license=("GPL3")
arch=('i686' 'x86_64' 'armv6h' 'armv7h' 'aarch64')
depends=('gtk3' 'libhandy' 'gnome-desktop' 'gnome-session'
         'upower-mobile' 'libpulse' 'gcr' 'feedbackd' 'libnm'
         'phoc>=0.4.4') # 'gnome-shell')
makedepends=('meson' 'git')
source=("git+https://source.puri.sm/Librem5/phosh.git#commit=${_commit}"
        "git+https://gitlab.gnome.org/GNOME/libgnome-volume-control.git"
         0001-system-prompt-allow-blank-passwords.patch
         0002-shell-honor-configured-rotation.patch
         0003-shell-add-an-accessor-for-the-mode-manager.patch
         0004-lockscreen-manager-undo-transform-only-for-phones.patch
	 "pam_phosh"
	 "sm.puri.OSK0.desktop"
         "phosh.service")
sha256sums=('SKIP'
            'SKIP'
            '0c5a2dbd0512ab8eca6e667f04ba03ec1b0d2896237b10d239aca63cfc19919e'
            '3a0189e044d0f976095c22c788317675e9cad457395ac234127b9c50669cec16'
            'b77fd656bc5b0a367bce4980b374654c747f391271a41a5cfdc8b3c317d31b1f'
            '2cb8240edf738d61f716d9f537902600a8f2ac14d0610f30086b32e7f3ea897c'
            'b7793f80c533e84ad8adfe8bb46c69f107575e724aa9b53b41f370baa37e4fd5'
            'f0faa73bb7793b7628b6a4ea8ab0059e13f5d46435efee2f4b8d8ac256311372'
            'ed6fac614d6799e3a1e0cdcff6ccf8d446c3b53c607dc3e93f1262d6b05843cc')

pkgver() {
  cd $pkgname
  git describe --tags | sed 's/^v//;s/-/+/g'
}

# Note: revert f70af466ff57763bc94548e3086a3caa50c8eacc if you don't want gnome-shell as dependency

_reverts=(
  f70af466ff57763bc94548e3086a3caa50c8eacc
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

	install -Dm644 "$srcdir"/phosh.service \
		"$pkgdir"/usr/lib/systemd/system/phosh.service
	install -Dm644 "$srcdir"/pam_phosh \
		"$pkgdir"/etc/pam.d/phosh
	install -Dm644 "$srcdir"/sm.puri.OSK0.desktop \
		"$pkgdir"/usr/share/applications/sm.puri.OSK0.desktop
}
