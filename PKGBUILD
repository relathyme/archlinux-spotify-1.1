# Maintainer: Christian Heusel <christian@heusel.eu>
# Maintainer: Robin Candau <antiz@archlinux.org>
# Contributor: NicoHood <archlinux {cat} nicohood {dog} de>
# Contributor: TobFromme < TobFromme {hat} pm {dont} me >
# Contributor: Ashley Whetter <(firstname) @ awhetter.co.uk>
# Contributor: Eothred <yngve.levinsen@gmail.com>

pkgname=spotify-1.1
pkgver='1.1.84.716'
_commit=gc5f8b819
pkgrel=2
pkgdesc='A proprietary music streaming service (old UI)'
arch=('x86_64')
license=('custom')
url='https://www.spotify.com'
depends=('alsa-lib>=1.0.14' 'gtk3' 'libxss' 'desktop-file-utils' 'openssl' 'nss' 'at-spi2-atk' 'libcurl-gnutls' 'libsm')
optdepends=('ffmpeg4.4: Adds support for playback of local files'
            'zenity: Adds support for importing local files'
            'libnotify: Desktop notifications')
makedepends=("squashfs-tools")
options=('!strip')
conflicts=('spotify')

source=('spotify.protocol'
        'LICENSE'
        "spotify-${pkgver}.${_commit}.snap::http://api.snapcraft.io/api/v1/snaps/download/pOBIoZ2LrCB3rDohMxoYGnbN14EHOgD7_60.snap")
        # snap source: https://github.com/flathub/com.spotify.Client/blob/221c019f44ee838de7128c34992f6d462bb75d5a/com.spotify.Client.json#L218

sha512sums=('999abe46766a4101e27477f5c9f69394a4bb5c097e2e048ec2c6cb93dfa1743eb436bde3768af6ba1b90eaac78ea8589d82e621f9cbe7d9ab3f41acee6e8ca20'
            '2e16f7c7b09e9ecefaa11ab38eb7a792c62ae6f33d95ab1ff46d68995316324d8c5287b0d9ce142d1cf15158e61f594e930260abb8155467af8bc25779960615'
            '1209b956822d8bb661daa2c88616bed403ec26dc22c6b866cecff59235c56112284c2f99aa06352fc0df6fcd15225a6ad60afd3b4ff4d7b948ab83e70ab31a71')

package() {
    cd "${srcdir}"

    unsquashfs -f -d $pkgdir spotify-$pkgver.$_commit.snap

    # cleanup to match AUR spotify package
    rm -rf $pkgdir/{lib,gnome-platform,data-dir,snap,etc,var,meta}
    rm -rf $pkgdir/usr/{bin/*,lib}
    rm -rf $pkgdir/usr/share/{X11,apport,bug,doc-base,fonts,glib-2.0,libdrm,libthai,lintian,locale,man,mime,pkgconfig,xml}
    find $pkgdir/usr/share/doc/ -mindepth 1 -type d ! -name spotify-client -exec rm -rf {} +
    find $pkgdir/usr/share/doc/ -type l ! -delete

    # Enable spotify to open URLs from the webapp
    sed -i 's/^Exec=.*/Exec=spotify --uri=%U/' "${pkgdir}"/usr/share/spotify/spotify.desktop

    # Fix desktop icon
    sed -i "s/^Icon=.*/Icon=spotify-client/g" $pkgdir/usr/share/spotify/spotify.desktop

    install -Dm644 "${pkgdir}"/usr/share/spotify/spotify.desktop "${pkgdir}"/usr/share/applications/spotify.desktop
    install -Dm644 "${pkgdir}"/usr/share/spotify/icons/spotify-linux-512.png "${pkgdir}"/usr/share/pixmaps/spotify-client.png

    for size in 22 24 32 48 64 128 256 512; do
        install -Dm644 "${pkgdir}/usr/share/spotify/icons/spotify-linux-$size.png" \
            "${pkgdir}/usr/share/icons/hicolor/${size}x${size}/apps/spotify.png"
    done

    # Move spotify binary to its proper location
    mkdir -p "${pkgdir}"/opt/spotify
    mv "${pkgdir}/usr/share/spotify" "${pkgdir}/opt/"

    # Symlink spotify binary which is located in /opt
    ln -sf /opt/spotify/spotify "${pkgdir}/usr/bin/spotify"

    # Copy protocol file for KDE
    install -Dm644 "${srcdir}/spotify.protocol" "${pkgdir}/usr/share/kservices5/spotify.protocol"

    # Install license
    # https://www.spotify.com/legal/end-user-agreement
    install -Dm 644 LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"

    # Fix permissions
    chmod -R go-w "${pkgdir}"
}
