# Maintainer. Dan Johansen <strit@manjaro.org>
# Contributor: Philip Goto <philip.goto@gmail.com>

pkgname=phosh
pkgver=0.4.5+13+gd0be671
_commit=c93fab7e32b7286ba3665718aed05b25066a023b
pkgrel=2
pkgdesc="A pure Wayland shell prototype for GNOME on mobile devices"
url="https://source.puri.sm/Librem5/phosh"
license=("GPL3")
arch=('i686' 'x86_64' 'armv6h' 'armv7h' 'aarch64')
depends=('gtk3' 'libhandy' 'gnome-desktop' 'gnome-session' 'upower' 'libpulse' 'gcr' 'feedbackd' 'libnm' 'phoc')
makedepends=('meson' 'git')
source=("git+https://source.puri.sm/Librem5/phosh.git#commit=${_commit}"
        "git+https://gitlab.gnome.org/GNOME/libgnome-volume-control.git"
         0001-system-prompt-allow-blank-passwords.patch
         0001-showing-unlock-page-re-orients-the-screen.patch
         MR631.patch
         #MR640.patch
         #MR641.patch
         flashlight-torch.patch
	 "pam_phosh"
	 "sm.puri.OSK0.desktop"
         "phosh.service")
sha256sums=('SKIP'
            'SKIP'
            '0c5a2dbd0512ab8eca6e667f04ba03ec1b0d2896237b10d239aca63cfc19919e'
            '674eb4f0b02c1eed1ab6af911a0890766d997d95edcd74bab0d36b9b64700cf2'
            'c006e410be625193cf1695b2dcc0464403a994e15f1a40046c6102463b72f2ac'
            '8b4ae3f08fc581aa8017d7b40ece6dd642697f2d683b408a1fceb7b2f2a81884'
            'b7793f80c533e84ad8adfe8bb46c69f107575e724aa9b53b41f370baa37e4fd5'
            'f0faa73bb7793b7628b6a4ea8ab0059e13f5d46435efee2f4b8d8ac256311372'
            'ed6fac614d6799e3a1e0cdcff6ccf8d446c3b53c607dc3e93f1262d6b05843cc')

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
