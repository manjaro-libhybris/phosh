# Maintainer: Bardia Moshiri <fakeshell@bardia.tech>
# Contributor: Erik Inkinen <erik.inkinen@gmail.com>
# Contributor: Dan Johansen <strit@manjaro.org>
# Contributor: Philip Goto <philip.goto@gmail.com>

pkgname=phosh-hybris
provides=(phosh)
conflicts=(phosh)
pkgver=0.25.0+10+gd0b9dcd4
_commit=d0b9dcd4506289f2efad22a43333da58b185ae66
pkgrel=1
pkgdesc="A pure Wayland shell prototype for GNOME on mobile devices"
url="https://gitlab.gnome.org/World/Phosh/phosh"
license=("GPL3")
arch=('i686' 'x86_64' 'armv6h' 'armv7h' 'aarch64')
depends=('gtk3' 'libhandy>=1.1.90' 'gnome-desktop' 'gnome-session'
         'upower' 'libpulse' 'gcr' 'feedbackd' 'libnm'
         'phoc-hybris>=0.24.0' 'gnome-shell' 'callaudiod-hybris')
makedepends=('meson' 'git')
source=("git+https://gitlab.gnome.org/World/Phosh/phosh.git#commit=${_commit}"
        "git+https://gitlab.gnome.org/GNOME/libgnome-volume-control.git"
        "git+https://gitlab.gnome.org/World/Phosh/libcall-ui.git"
         0001-system-prompt-allow-blank-passwords.patch
         0002-fix-locale-issue.patch
         0003-fix-locale-issue-in-service-file.patch
         0004-fix-hwcomposer.patch
         0005-fix-plugins.patch
         https://gitlab.gnome.org/World/Phosh/phosh/-/merge_requests/977.patch
         "pam_phosh"
         "sm.puri.OSK0.desktop")
sha256sums=('SKIP'
            'SKIP'
            'SKIP'
            '0c5a2dbd0512ab8eca6e667f04ba03ec1b0d2896237b10d239aca63cfc19919e'
            'e95cc671a0926afe3fe0459bcd2b87a114091000ea465b4e4b5a15f6322d9459'
            '35d01e5d5801d8af7521a37186f92ba0b7784443585aae07f64a58207b050b5a'
            '385f643453018e6ca1418b8594fcacbd79edb8e45a41491f63d2d621555ffb49'
            'b21bbeb0698e34bf16d38b3392a81775ed69b7caea0fe1aa3e5b67e00be1c024'
            '2a910bdb4592ccb89f6bf93adf7d313428801c774115055eb92e1f7242ca1e0d'
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
