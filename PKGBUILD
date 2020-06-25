# Maintainer: Matthew <mattzilvar@gmail.com>
# Contributors: Seppia <seppia@seppio.fish>
# Contributors: Eloston
# Derived from official Chromium and Inox PKGBUILDS and ungoogled-chromium buildkit

pkgname=ungoogled-chromium
pkgver=83.0.4103.97
pkgrel=1
# sometimes an ungoogled patches can be combined with a new chromium release
# only if the release only includes security fixes
_ungoogled_ver=83.0.4103.97-1
_launcher_ver=6
pkgdesc="A lightweight approach to removing Google web service dependency"
arch=('aarch64')
url="https://github.com/Eloston/ungoogled-chromium"
license=('BSD')
depends=('gtk3' 'nss' 'alsa-lib' 'xdg-utils' 'libxss' 'libcups' 'libgcrypt'
         'ttf-liberation' 'systemd' 'dbus' 'libpulse' 'pciutils' 'json-glib' 'libva'
         'desktop-file-utils' 'hicolor-icon-theme')
makedepends=('python' 'python2' 'gperf' 'yasm' 'mesa' 'ninja' 'nodejs' 'git'
             'libpipewire02' 'clang' 'lld' 'gn' 'java-runtime-headless'
             'python2-setuptools')
optdepends=('pepper-flash: support for Flash content'
            'libpipewire02: WebRTC desktop sharing under Wayland'
            'kdialog: needed for file dialogs in KDE'
            'org.freedesktop.secrets: password storage backend on GNOME / Xfce'
            'kwallet: for storing passwords in KWallet on KDE desktops')
provides=('chromium')
conflicts=('chromium')
install=chromium.install
source=(https://commondatastorage.googleapis.com/chromium-browser-official/chromium-$pkgver.tar.xz
        https://github.com/Eloston/ungoogled-chromium/archive/$_ungoogled_ver.tar.gz
        chromium-launcher-$_launcher_ver.tar.gz::https://github.com/foutrelis/chromium-launcher/archive/v$_launcher_ver.tar.gz
        chromium-drirc-disable-10bpc-color-configs.conf
        clean-up-a-call-to-set_utf8.patch
        iwyu-std-numeric_limits-is-defined-in-limits.patch
        add-missing-algorithm-header-in-crx_install_error.cc.patch
        libstdc-fix-incomplete-type-in-AXTree-for-NodeSetSiz.patch
        include-memory-header-to-get-the-definition-of-std-u.patch
        make-some-of-blink-custom-iterators-STL-compatible.patch
        avoid-double-destruction-of-ServiceWorkerObjectHost.patch
        v8-remove-soon-to-be-removed-getAllFieldPositions.patch
        chromium-skia-harmony.patch
        0001-crashpad-include-limits.patch)
sha256sums=('12c405f61284cfc78f8c2b6600f3c1ae61a83b639c41087bb4f74fcaab036f83'
            '21767f015d14d024884b8780a975f0d8b4b2e31dd11d5eaec6a09938a5a2480f'
            '04917e3cd4307d8e31bfb0027a5dce6d086edb10ff8a716024fbb8bb0c7dccf1'
            'babda4f5c1179825797496898d77334ac067149cac03d797ab27ac69671a7feb'
            '58c41713eb6fb33b6eef120f4324fa1fb8123b1fbc4ecbe5662f1f9779b9b6af'
            '675fb3d6276cce569a641436465f58d5709d1d4a5f62b7052989479fd4aaea24'
            '0e2a78e4aa7272ab0ff4a4c467750e01bad692a026ad9828aaf06d2a9418b9d8'
            '50687079426094f2056d1f4806dc30fc8d6bad16190520e57ba087ec5db1d778'
            '071326135bc25226aa165639dff80a03670a17548f2d2ff5cc4f40982b39c52a'
            '3d7f20e1d2ee7d73ed25e708c0d59a0cb215fcce10a379e3d48a856533c4b0b7'
            'd793842e9584bf75e3779918297ba0ffa6dd05394ef5b2bf5fb73aa9c86a7e2f'
            'e042024423027ad3ef729a7e4709bdf9714aea49d64cfbbf46a645a05703abc2'
            '771292942c0901092a402cc60ee883877a99fb804cb54d568c8c6c94565a48e1'
            'df99f49ad58b70c9a3e1827d7e80b62e4363419334ed83373cf55b79c17b6f10')

# Possible replacements are listed in build/linux/unbundle/replace_gn_files.py
# Keys are the names in the above script; values are the dependencies in Arch
declare -gA _system_libs=(
  [ffmpeg]=ffmpeg
  [flac]=flac
  [fontconfig]=fontconfig
  [freetype]=freetype2
  [harfbuzz-ng]=harfbuzz
  [icu]=icu
  [libdrm]=
  [libjpeg]=libjpeg
  #[libpng]=libpng    # https://crbug.com/752403#c10
  [libvpx]=libvpx
  [libwebp]=libwebp
  [libxml]=libxml2
  [libxslt]=libxslt
  [opus]=opus
  [re2]=re2
  [snappy]=snappy
  [yasm]=
  [zlib]=minizip
)
_unwanted_bundled_libs=(
  $(printf "%s\n" ${!_system_libs[@]} | sed 's/^libjpeg$/&_turbo/')
)
depends+=(${_system_libs[@]})

prepare() {
  cd "$srcdir/chromium-$pkgver"

  # Arch Linux ARM fixes
  patch -p1 -i ../0001-crashpad-include-limits.patch
  
  # Allow build to set march and options on AArch64 (crc, crypto)
  [[ $CARCH == "aarch64" ]] && CFLAGS=`echo $CFLAGS | sed -e 's/-march=armv8-a//'` && CXXFLAGS="$CFLAGS"

  # Allow building against system libraries in official builds
  sed -i 's/OFFICIAL_BUILD/GOOGLE_CHROME_BUILD/' \
    tools/generate_shim_headers/generate_shim_headers.py

  # https://crbug.com/893950
  sed -i -e 's/\<xmlMalloc\>/malloc/' -e 's/\<xmlFree\>/free/' \
    third_party/blink/renderer/core/xml/*.cc \
    third_party/blink/renderer/core/xml/parser/xml_document_parser.cc \
    third_party/libxml/chromium/*.cc

  # https://chromium-review.googlesource.com/c/chromium/src/+/2145261
  patch -Np1 -i ../clean-up-a-call-to-set_utf8.patch

  # https://chromium-review.googlesource.com/c/chromium/src/+/2153111
  patch -Np1 -F3 -i ../iwyu-std-numeric_limits-is-defined-in-limits.patch

  # https://chromium-review.googlesource.com/c/chromium/src/+/2152333
  patch -Np1 -i ../add-missing-algorithm-header-in-crx_install_error.cc.patch

  # https://chromium-review.googlesource.com/c/chromium/src/+/2132403
  patch -Np1 -i ../libstdc-fix-incomplete-type-in-AXTree-for-NodeSetSiz.patch

  # https://chromium-review.googlesource.com/c/chromium/src/+/2164645
  patch -Np1 -i ../include-memory-header-to-get-the-definition-of-std-u.patch

  # https://chromium-review.googlesource.com/c/chromium/src/+/2174199
  patch -Np1 -i ../make-some-of-blink-custom-iterators-STL-compatible.patch

  # https://chromium-review.googlesource.com/c/chromium/src/+/2094496
  patch -Np1 -i ../avoid-double-destruction-of-ServiceWorkerObjectHost.patch

  # https://crbug.com/v8/10393
  patch -Np1 -d v8 <../v8-remove-soon-to-be-removed-getAllFieldPositions.patch

  # https://crbug.com/skia/6663#c10
  patch -Np0 -i ../chromium-skia-harmony.patch

  # Ungoogled Chromium changes
  _ungoogled_repo="$srcdir/$pkgname-$_ungoogled_ver"
  _utils="${_ungoogled_repo}/utils"
  msg2 'Pruning binaries'
  python "$_utils/prune_binaries.py" ./ "$_ungoogled_repo/pruning.list"
  msg2 'Applying patches'
  python "$_utils/patches.py" apply ./ "$_ungoogled_repo/patches"
  msg2 'Applying domain substitution'
  python "$_utils/domain_substitution.py" apply -r "$_ungoogled_repo/domain_regex.list" -f "$_ungoogled_repo/domain_substitution.list" -c domainsubcache.tar.gz ./

  # Force script incompatible with Python 3 to use /usr/bin/python2
  sed -i '1s|python$|&2|' third_party/dom_distiller_js/protoc_plugins/*.py

  mkdir -p third_party/node/linux/node-linux-x64/bin
  ln -s /usr/bin/node third_party/node/linux/node-linux-x64/bin/

  # Remove bundled libraries for which we will use the system copies; this
  # *should* do what the remove_bundled_libraries.py script does, with the
  # added benefit of not having to list all the remaining libraries
  local _lib
  for _lib in ${_unwanted_bundled_libs[@]}; do
    find "third_party/$_lib" -type f \
      \! -path "third_party/$_lib/chromium/*" \
      \! -path "third_party/$_lib/google/*" \
      \! -path "third_party/harfbuzz-ng/utils/hb_scoped.h" \
      \! -path 'third_party/yasm/run_yasm.py' \
      \! -regex '.*\.\(gn\|gni\|isolate\)' \
      -delete
  done

  python2 build/linux/unbundle/replace_gn_files.py \
    --system-libraries "${!_system_libs[@]}"
}

build() {
  make -C chromium-launcher-$_launcher_ver

  cd "$srcdir/chromium-$pkgver"

  if check_buildoption ccache y; then
    # Avoid falling back to preprocessor mode when sources contain time macros
    export CCACHE_SLOPPINESS=time_macros
  fi

  export CC=clang
  export CXX=clang++
  export AR=ar
  export NM=nm

  local _flags=(
    'custom_toolchain="//build/toolchain/linux/unbundle:default"'
    'host_toolchain="//build/toolchain/linux/unbundle:default"'
    'is_official_build=true' # implies is_cfi=true on x86_64
    'ffmpeg_branding="Chrome"'
    'proprietary_codecs=true'
    'rtc_use_pipewire=true'
    'link_pulseaudio=true'
    'use_gnome_keyring=false'
    'use_sysroot=false'
    'linux_use_bundled_binutils=false'
    'use_custom_libcxx=false'
    'use_vaapi=true'
  )

  if [[ -n ${_system_libs[icu]+set} ]]; then
    _flags+=('icu_use_data_file=false')
  fi

  if check_option strip y; then
    _flags+=('symbol_level=0')
  fi

  # Append ungoogled chromium flags to _flags array
  _ungoogled_repo="$srcdir/$pkgname-$_ungoogled_ver"
  readarray -t -O ${#_flags[@]} _flags < "${_ungoogled_repo}/flags.gn"

  # Facilitate deterministic builds (taken from build/config/compiler/BUILD.gn)
  CFLAGS+='   -Wno-builtin-macro-redefined'
  CXXFLAGS+=' -Wno-builtin-macro-redefined'
  CPPFLAGS+=' -D__DATE__=  -D__TIME__=  -D__TIMESTAMP__='

  # Do not warn about unknown warning options
  CFLAGS+='   -Wno-unknown-warning-option'
  CXXFLAGS+=' -Wno-unknown-warning-option'

  msg2 'Configuring Chromium'
  gn gen out/Release --args="${_flags[*]}" --script-executable=/usr/bin/python2
  msg2 'Building Chromium'
  ninja -C out/Release chrome chrome_sandbox chromedriver
}

package() {
  cd chromium-launcher-$_launcher_ver
  make PREFIX=/usr DESTDIR="$pkgdir" install
  install -Dm644 LICENSE \
    "$pkgdir/usr/share/licenses/chromium/LICENSE.launcher"

  cd "$srcdir/chromium-$pkgver"

  install -D out/Release/chrome "$pkgdir/usr/lib/chromium/chromium"
  install -Dm4755 out/Release/chrome_sandbox "$pkgdir/usr/lib/chromium/chrome-sandbox"
  ln -s /usr/lib/${pkgname#ungoogled-}/chromedriver "$pkgdir/usr/bin/chromedriver"

  install -Dm644 ../chromium-drirc-disable-10bpc-color-configs.conf \
    "$pkgdir/usr/share/drirc.d/10-$pkgname.conf"

  install -Dm644 chrome/installer/linux/common/desktop.template \
    "$pkgdir/usr/share/applications/chromium.desktop"
  install -Dm644 chrome/app/resources/manpage.1.in \
    "$pkgdir/usr/share/man/man1/chromium.1"
  sed -i \
    -e 's/@@MENUNAME@@/Chromium/g' \
    -e 's/@@PACKAGE@@/chromium/g' \
    -e 's/@@USR_BIN_SYMLINK_NAME@@/chromium/g' \
    "$pkgdir/usr/share/applications/chromium.desktop" \
    "$pkgdir/usr/share/man/man1/chromium.1"

  install -Dm644 chrome/installer/linux/common/chromium-browser/chromium-browser.appdata.xml \
    "$pkgdir/usr/share/metainfo/chromium.appdata.xml"
  sed -ni \
    -e 's/chromium-browser\.desktop/chromium.desktop/' \
    -e '/<update_contact>/d' \
    -e '/<p>/N;/<p>\n.*\(We invite\|Chromium supports Vorbis\)/,/<\/p>/d' \
    -e '/^<?xml/,$p' \
    "$pkgdir/usr/share/metainfo/chromium.appdata.xml"

  local toplevel_files=(
    chrome_100_percent.pak
    chrome_200_percent.pak
    resources.pak
    v8_context_snapshot.bin

    # ANGLE
    libEGL.so
    libGLESv2.so

    chromedriver
  )

  if [[ -z ${_system_libs[icu]+set} ]]; then
    toplevel_files+=(icudtl.dat)
  fi

  cp "${toplevel_files[@]/#/out/Release/}" "$pkgdir/usr/lib/chromium/"
  install -Dm644 -t "$pkgdir/usr/lib/chromium/locales" out/Release/locales/*.pak
  install -Dm755 -t "$pkgdir/usr/lib/chromium/swiftshader" out/Release/swiftshader/*.so

  for size in 24 48 64 128 256; do
    install -Dm644 "chrome/app/theme/chromium/product_logo_$size.png" \
      "$pkgdir/usr/share/icons/hicolor/${size}x${size}/apps/chromium.png"
  done

  for size in 16 32; do
    install -Dm644 "chrome/app/theme/default_100_percent/chromium/product_logo_$size.png" \
      "$pkgdir/usr/share/icons/hicolor/${size}x${size}/apps/chromium.png"
  done

  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/chromium/LICENSE"
}

# vim:set ts=2 sw=2 et ft=sh:
