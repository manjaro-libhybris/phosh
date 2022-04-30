# Maintainer: Erik Inkinen <erik.inkinen@gmail.com>
# Contributor: Dan Johansen <strit@manjaro.org>
# Contributor: Philip Goto <philip.goto@gmail.com>

pkgname=phosh-hybris
provides=(phosh)
conflicts=(phosh)
pkgver=0.17.0+113+gad03627e
_commit=ad03627ed13b39a2903ea5c9642418ab1a3c5f0b
pkgrel=1
pkgdesc="A pure Wayland shell prototype for GNOME on mobile devices"
url="https://gitlab.gnome.org/World/Phosh/phosh"
license=("GPL3")
arch=('i686' 'x86_64' 'armv6h' 'armv7h' 'aarch64')
depends=('gtk3' 'libhandy>=1.1.90' 'gnome-desktop' 'gnome-session'
         'upower' 'libpulse' 'gcr' 'feedbackd' 'libnm'
         'phoc-hybris>=0.8.0' 'gnome-shell' 'callaudiod-hybris')
makedepends=('meson' 'git')
source=("git+https://gitlab.gnome.org/World/Phosh/phosh.git#commit=${_commit}"
        "git+https://gitlab.gnome.org/GNOME/libgnome-volume-control.git"
        "git+https://gitlab.gnome.org/World/Phosh/libcall-ui.git"
         0001-system-prompt-allow-blank-passwords.patch
         0002-fix-locale-issue.patch
         0003-fix-locale-issue-in-service-file.patch
         0004-fix-hwcomposer.patch
         https://gitlab.gnome.org/World/Phosh/phosh/-/merge_requests/977.patch
         https://gitlab.gnome.org/World/Phosh/phosh/-/merge_requests/1008.patch
         "pam_phosh"
         "sm.puri.OSK0.desktop")
sha256sums=('SKIP'
            'SKIP'
            'SKIP'
            '0c5a2dbd0512ab8eca6e667f04ba03ec1b0d2896237b10d239aca63cfc19919e'
            'b1f9083be8d1cf259a097b47c3fba4f639d597dad2a46e4234dd9c0cd2391bc2'
            '35d01e5d5801d8af7521a37186f92ba0b7784443585aae07f64a58207b050b5a'
            '385f643453018e6ca1418b8594fcacbd79edb8e45a41491f63d2d621555ffb49'
            '5d30e23a5327b3cc3056f06f0d3bcfaf856ea2de1b48e96d44e7a0d8361e3c81'
            '07a911db44f17dad5d66b4b64ef7498eccd480102dd096fa531a0ff1cf837b59'
            'b7793f80c533e84ad8adfe8bb46c69f107575e724aa9b53b41f370baa37e4fd5'
            'f0faa73bb7793b7628b6a4ea8ab0059e13f5d46435efee2f4b8d8ac256311372')

pkgver() {
  cd phosh
  git describe --tags | sed 's/^v//;s/-/+/g'
}

_reverts=(
)

prepare() {
    cd phosh

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

	install -Dm644 "$srcdir"/phosh/data/phosh.service \
		"$pkgdir"/usr/lib/systemd/system/phosh.service
	install -Dm644 "$srcdir"/pam_phosh \
		"$pkgdir"/etc/pam.d/phosh
	install -Dm644 "$srcdir"/sm.puri.OSK0.desktop \
		"$pkgdir"/usr/share/applications/sm.puri.OSK0.desktop
}
