# Maintainer. Dan Johansen <strit@manjaro.org>
# Contributor: Philip Goto <philip.goto@gmail.com>

pkgname=phosh
pkgver=0.4.4+74+g9ba7f11
_commit=9ba7f117cb5c941a02b4310c51c8941dd4e1d00f
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
         #0002-monitor-use-a-floating-point-scale-factor.patch
         #0001-rotate-MR-434.patch
         #0001-rotate-MR-434-fix-PP.patch
         #0001-showing-unlock-page-re-orients-the-screen.patch
         MR490.patch
         MR630.patch
         MR631.patch
         0001-flashlight-initial-addition.patch
         0002-flashlight-compiles.patch
         0003-flashlight-responses.patch
         0004-flashlight-functional.patch
	 "pam_phosh"
	 "sm.puri.OSK0.desktop"
         "phosh.service")
sha256sums=('SKIP'
            'SKIP'
            '0c5a2dbd0512ab8eca6e667f04ba03ec1b0d2896237b10d239aca63cfc19919e'
            'f9d62f004e9763d96ab4a18eb3f6be2ea3f0e43e670cad3a9009173ce9f70e5e'
            '41abd65619ae0f4faec0963ad8cf97d143c58cba53859c5295e66e2a16990a0f'
            'f4e4108e19f96477a3c470f4b62b8744782c84335d85f6b315f198e8c433dc85'
            'beb6ca3f38fcb217ceebf40cc5207fc4a9cabc9bb2eb43b1350e961e793a3e66'
            '314476e29858f04ce6b40322dfe311279e5494c783617ec889c0c20aad7f82ac'
            'de7f545dbdc3da2a71530899dddb559c88d2fc6a5b5fc565bfe34535aec08601'
            'b3a676b489fd6fb0e0c682248d96858e76d0736cfa0796f20052409e25c3b6ef'
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
