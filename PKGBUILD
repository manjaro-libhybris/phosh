# Maintainer. Dan Johansen <strit@manjaro.org>
# Contributor: Philip Goto <philip.goto@gmail.com>

pkgname=phosh
pkgver=0.9.0+12+ga913752
_commit=fa16c3706b383d85098d6fdfe914c4ff84787ef5
pkgrel=2
pkgdesc="A pure Wayland shell prototype for GNOME on mobile devices"
url="https://source.puri.sm/Librem5/phosh"
license=("GPL3")
arch=('i686' 'x86_64' 'armv6h' 'armv7h' 'aarch64')
depends=('gtk3' 'libhandy>=1.1.90' 'gnome-desktop' 'gnome-session'
         'upower-mobile' 'libpulse' 'gcr' 'feedbackd' 'libnm'
         'phoc>=0.4.4' 'gnome-shell')
makedepends=('meson' 'git')
source=(#"git+https://source.puri.sm/Librem5/phosh.git#commit=${_commit}"
        "git+https://source.puri.sm/philm/phosh.git#commit=${_commit}"
        "git+https://gitlab.gnome.org/GNOME/libgnome-volume-control.git"
         0001-system-prompt-allow-blank-passwords.patch
         0002-only-lock-when-not-near.patch
         MR434.patch
         MR434-fix-rotation-on-a-PP.patch
	 "pam_phosh"
	 "sm.puri.OSK0.desktop"
         "phosh.service")
sha256sums=('SKIP'
            'SKIP'
            '0c5a2dbd0512ab8eca6e667f04ba03ec1b0d2896237b10d239aca63cfc19919e'
            '5053abfc106998dfba4ba9b90a1dadea4ef69fd1e0e1e6cbc632aac76106a3d9'
            '4266cf51e66f9c0bc3f42d517f1422ef11af385983ade3328bd8316114e5ad8a'
            '76607a3c283546a072965a444bfc1d5f99d74576b27627fa5ecfd670d5da21ad'
            'b7793f80c533e84ad8adfe8bb46c69f107575e724aa9b53b41f370baa37e4fd5'
            'f0faa73bb7793b7628b6a4ea8ab0059e13f5d46435efee2f4b8d8ac256311372'
            'ed6fac614d6799e3a1e0cdcff6ccf8d446c3b53c607dc3e93f1262d6b05843cc')

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
