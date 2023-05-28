pkgname="dern"
pkgver="0.0.3"
pkgrel="1"
pkgdesc="anonymous volatile chat platform"
arch=("x86_64")
depends=("nodejs" "yarn" "tor")
makedepends=("nvm")
license=("MIT")
source=("exertive-ts-${pkgver}::https://github.com/dern-chat/exertive-ts/archive/refs/tags/v${pkgver}.tar.gz"
        "chatter-react-${pkgver}::https://github.com/dern-chat/chatter-react/archive/refs/tags/v${pkgver}.tar.gz"
)
sha256sums=("SKIP"
            "SKIP"
)

_ensure_local_nvm() {
  # let's be sure we are starting clean
  which nvm >/dev/null 2>&1 && nvm deactivate && nvm unload
  mkdir -p "${pkgdir}/opt/dern"
  export NVM_DIR="${pkgdir}/opt/dern/.nvm"
  # The init script returns 3 if version specified
  # in ./.nvrc is not (yet) installed in $NVM_DIR
  # but nvm itself still gets loaded ok
  source /usr/share/nvm/init-nvm.sh || [[ $? != 1 ]]
}

package() {
    cd "${srcdir}/exertive-ts-${pkgver}"
    _ensure_local_nvm
    nvm install
    
    cd "${srcdir}/chatter-react-${pkgver}"
    _ensure_local_nvm
    nvm use
    yarn
    yarn build
    cp -r "${srcdir}/chatter-react-${pkgver}/dist" "${srcdir}/exertive-ts-${pkgver}/public"
    
    cd "${srcdir}/exertive-ts-${pkgver}"
    yarn
    yarn build

    mkdir -p "${pkgdir}/opt/dern"
    cp -r "${srcdir}/exertive-ts-${pkgver}" "${pkgdir}/opt/dern"
    mkdir -p "${pkgdir}/usr/bin"
    touch "${srcdir}/dern.sh"
    echo "cd /opt/dern/exertive-ts-${pkgver}" >> "${srcdir}/dern.sh"
    echo "(tor -f torrc --quiet &)" >> "${srcdir}/dern.sh"
    echo "domain=\$(cat /var/lib/tor/dern/hostname)" >> "${srcdir}/dern.sh"
    echo "echo onion domain: \$domain" >> "${srcdir}/dern.sh"
    echo "sed -i 's+http://127.0.0.1:2323+'"http://\$domain"'+g' /opt/dern/exertive-ts-${pkgver}/dist/public/assets/index-*.js" >> "${srcdir}/dern.sh"
    echo "sed -i 's+http://127.0.0.1:2323+'"http://\$domain"'+g' /opt/dern/exertive-ts-${pkgver}/public/assets/index-*.js" >> "${srcdir}/dern.sh"
    echo "yarn start"  >> "${srcdir}/dern.sh"
    cp "${srcdir}/dern.sh" "${pkgdir}/usr/bin/dern"

    touch "${srcdir}/torrc"
    echo "HiddenServiceDir /var/lib/tor/dern" >> "${srcdir}/torrc"
    echo "HiddenServicePort 80 127.0.0.1:2323" >> "${srcdir}/torrc"
    cp "${srcdir}/torrc" "${pkgdir}/opt/dern/exertive-ts-${pkgver}/torrc"

    chmod +x "${pkgdir}/usr/bin/dern"
}