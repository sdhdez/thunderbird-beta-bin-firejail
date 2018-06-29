# Maintainer: Det <nimetonmaili g-mail>
# Based on [extra]'s thunderbird

pkgname=thunderbird-beta-bin
_pkgname=thunderbird-beta
pkgver=60.0b9
_major=${pkgver/rc*}
_build=${pkgver/*rc}
pkgrel=1
pkgdesc="Standalone Mail/News reader - Bleeding edge binary version"
arch=('x86_64')
url="https://www.mozilla.org/thunderbird"
license=('GPL' 'LGPL' 'MPL')
depends=('dbus-glib' 'gtk3' 'libxt' 'nss')
optdepends=('hunspell: Spell checking'
            'hyphen: Hyphenation'
            'libcanberra: Sound support')
provides=("thunderbird=$pkgver")
conflicts=('thunderbird-beta')
install=$pkgname.install
source=("https://ftp.mozilla.org/pub/thunderbird/releases/$pkgver/linux-x86_64/en-US/thunderbird-$pkgver.tar.bz2"
        'thunderbird-beta-bin.desktop'
        'vendor.js'
        'thunderbird-launcher.sh')
sha512sums=('a6a64fa11aaaa89862ed58cd5744ac3a6c0fa8e65958cf46fca5ba45329e145586597879fbd783522bcb9f0d4ad1f78c5566360e994bddf971073c409fcf8be1'
            '4f8dc75ce04f82fbd5c14898ca79767f3ad2c161eea7f9c1e6131787d25933301214fdb11f900d19f9d85f21c9d02c45d1c86b7c871ddfec8ccd85ba4bacf156'
            'aeb444784732267f1b1e87e6084a776f82a1912c4c2637d2cf1de1c135dd9d41d2ef66d2bd3f9cbd3a79fad32d17ea6e2968ba644d5f887cb66ba6c09a2098f5'
            'af2cd461895b3df5c015b4c46e8d7f107c654d5a1e383ada8c222f88630719f90de011cc774f80a5d9d8d435652c1fa5c1a4a0fa63556a0ac6e3591e56a2ea23')
# RC
if [[ $_build = ? ]]; then
  source[0]="thunderbird-$pkgver.tar.bz2::https://ftp.mozilla.org/pub/thunderbird/candidates/$_major-candidates/build$_build/linux-x86_64/en-US/thunderbird-$_major.tar.bz2"
fi

package() {
  # Create directories
  msg2 "Creating directory structure..."
  install -d "$pkgdir"/usr/bin
  install -d "$pkgdir"/usr/share/applications
  install -d "$pkgdir"/opt

  msg2 "Moving stuff in place..."
  # Install
  cp -r thunderbird/ "$pkgdir"/opt/$_pkgname

  # Launchers
  install -m755 thunderbird-launcher.sh "$pkgdir"/opt/$_pkgname/thunderbird-launcher
  ln -s /opt/$_pkgname/thunderbird-launcher "$pkgdir"/usr/bin/$_pkgname
  ln -sf thunderbird-launcher "$pkgdir"/opt/$_pkgname/thunderbird-bin

  # vendor.js
  _vendorjs="$pkgdir/opt/$_pkgname/defaults/preferences/vendor.js"
  install -Dm644 /dev/stdin "$_vendorjs" <<END
// Use LANG environment variable to choose locale
pref("intl.locale.matchOS", true);

// Disable default mailer checking.
pref("mail.shell.checkDefaultMail", false);

// Don't disable our bundled extensions in the application directory
pref("extensions.autoDisableScopes", 11);
pref("extensions.shownSelectionUI", true);
END

  # Desktop
  install -m644 $pkgname.desktop "$pkgdir"/usr/share/applications/

  # Icons
  for i in 16 22 24 32 48 256; do
    install -d "$pkgdir"/usr/share/icons/hicolor/${i}x${i}/apps/
    ln -s /opt/$_pkgname/chrome/icons/default/default$i.png \
          "$pkgdir"/usr/share/icons/hicolor/${i}x${i}/apps/$_pkgname.png
  done

  # Use system-provided dictionaries
  rm -r "$pkgdir"/opt/$_pkgname/dictionaries
  ln -Ts /usr/share/hunspell "$pkgdir"/opt/$_pkgname/dictionaries
  ln -Ts /usr/share/hyphen "$pkgdir"/opt/$_pkgname/hyphenation

  # Use system certificates
  ln -sf /usr/lib/libnssckbi.so "$pkgdir"/opt/$_pkgname/libnssckbi.so
}
