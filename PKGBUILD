pkgname="emacs-git"
pkgver=30.0.92.174409
pkgrel=1
pkgdesc="GNU Emacs. Development master branch."
arch=('x86_64')
url="http://www.gnu.org/software/emacs/"
license=('GPL3')
depends=('gnutls'
	 'libxml2'
	 'jansson'
	 'harfbuzz'
	 'libotf'
	 'libgccjit'
	 'libxi'
	 'gtk3'
	 'libsm'
	 'xcb-util'
	 'libxcb'
	 'libjpeg-turbo'
	 'libpng'
	 'giflib'
	 'libwebp'
	 'libtiff'
	 'libxpm'
	 'cairo'
	 'tree-sitter'
	 'sqlite3')
makedepends=('git' 'xorgproto' 'libxi')
provides=('emacs')
conflicts=('emacs')
source=("emacs-git::git+https://git.savannah.gnu.org/git/emacs.git#branch=emacs-30")
options=(!strip)
b2sums=('SKIP')

pkgver() {
	cd "$srcdir/emacs-git"
	printf "%s.%s" \
	       $(grep AC_INIT configure.ac | \
			 awk -F',' '{ gsub("[ \\[\\]]","",$2); print $2 }') \
	       $(git rev-list --count HEAD)
}

prepare() {
	cd "$srcdir/emacs-git"
	[[ -x configure ]] || ( ./autogen.sh git && ./autogen.sh autoconf )
	mkdir -p "$srcdir/emacs-git/build"
}

build() {
	local _confflags=(
		--prefix=/usr
		--sysconfdir=/etc
		--libexecdir=/usr/lib
		--localstatedir=/var
		--mandir=/usr/share/man
		--with-cairo
		--with-harfbuzz
		--with-native-compilation
		--with-tree-sitter
		--with-pgtk
		--without-compress-install
	)

	cd "$srcdir/emacs-git/build"
	../configure "${_confflags[@]}"
	make
}

package() {
	cd "$srcdir/emacs-git/build"

	make DESTDIR="$pkgdir/" install

	# fix user/root permissions on usr/share files
	find "$pkgdir"/usr/share/emacs/ | xargs chown root:root

	# fix permssions on /var/games
	mkdir -p "$pkgdir"/var/games/emacs
	chmod 775 "$pkgdir"/var/games
	chmod 775 "$pkgdir"/var/games/emacs
	chown -R root:games "$pkgdir"/var/games
}

post_install() {
	# fix user/root permissions on usr/share files
	find "$pkgdir"/usr/share/emacs/ | xargs chown root:root
	# make sure directory has the correct owner and group
	chown -R root:games "$pkgdir"/var/games
}
