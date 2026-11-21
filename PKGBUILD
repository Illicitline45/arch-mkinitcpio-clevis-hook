
pkgname=mkinitcpio-clevis-hook-git
pkgver=r1.0000000
pkgrel=1
pkgdesc="Mkinitcpio hook for automatic LUKS unlocking with Clevis and TPM2"
arch=('any')
url="https://github.com/Illicitline45/arch-mkinitcpio-clevis-hook"
license=('GPL-3.0-or-later')

depends=(
    'mkinitcpio'
    'clevis'
    'tpm2-tools'
    'luksmeta'
    'libpwquality'
)

makedepends=('git')

provides=('mkinitcpio-clevis-hook')
conflicts=('mkinitcpio-clevis-hook')

source=(
    "$pkgname::git+${url}.git#branch=main"
)

sha256sums=('SKIP')

pkgver() {
    cd "$srcdir/$pkgname"

    printf 'r%s.%s' \
        "$(git rev-list --count HEAD)" \
        "$(git rev-parse --short=7 HEAD)"
}

package() {
    cd "$srcdir/$pkgname"

    # Install the mkinitcpio runtime hook
    install -Dm644 hooks/clevis \
        "$pkgdir/usr/lib/initcpio/hooks/clevis"

    # Install the mkinitcpio build hook
    install -Dm644 install/clevis \
        "$pkgdir/usr/lib/initcpio/install/clevis"

    # Install documentation and license
    install -Dm644 README.md \
        "$pkgdir/usr/share/doc/$pkgname/README.md"

    install -Dm644 LICENSE \
        "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}
