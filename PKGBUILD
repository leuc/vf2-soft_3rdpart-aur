# Maintainer: Leuc <d.leucas at outlook com>
# 
# Hardware decoding and encoding packages for Starfive Vision Five 2 SBC
# 
# FIXME Patched Makefile's have a hard-coded "target" as work-around symlink to 
#
# Some patch files don't apply anymore
#
pkgname=('codaj12' 'wave420l' 'wave511' 'omx-il')
pkgbase="soft_3rdpart-VF2"
pkgver=3.6.1
pkgrel=1
arch=('riscv64')
url="https://github.com/starfive-tech/soft_3rdpart"
groups=('starfive-vf2-multimedia')

makedepends=('patch' 'git')
source=(
		"https://github.com/starfive-tech/soft_3rdpart/archive/refs/tags/VF2_v3.6.1.tar.gz"
        "https://github.com/starfive-tech/Debian/raw/v0.9.0-engineering-release-wayland/multimedia/patch/mm_libs/0001-wave511-fix-building-issue-and-support-the-vendor-co.patch"
		"https://github.com/starfive-tech/Debian/raw/v0.9.0-engineering-release-wayland/multimedia/patch/mm_libs/0002-wave420l-fix-building-issue-and-support-the-vendor-c.patch"
		"https://github.com/starfive-tech/Debian/raw/v0.9.0-engineering-release-wayland/multimedia/patch/mm_libs/0003-codaj12-fix-building-issue-and-support-the-vendor-co.patch"
		"https://github.com/starfive-tech/Debian/raw/v0.9.0-engineering-release-wayland/multimedia/patch/mm_libs/0004-omx-il-fix-building-issue-and-support-omx-test-examp.patch"
		#"https://github.com/starfive-tech/Debian/raw/v0.9.0-engineering-release-wayland/multimedia/patch/mm_libs/0005-wave511-fix-the-warning-which-treated-as-error.patch"
		#"https://github.com/starfive-tech/Debian/raw/v0.9.0-engineering-release-wayland/multimedia/patch/mm_libs/0006-wave420l-Fix-Werror-address-issue-or-add-Waddress-in.patch"
		#"https://github.com/starfive-tech/Debian/raw/v0.9.0-engineering-release-wayland/multimedia/patch/mm_libs/0007-omx-il-Fix-warning-issue-for-new-glibc.patch"
        )
md5sums=("7f4487f62245292a383e99865ee5b71b"
		 "e4bf8ad38b5eb3313abacc18ae8e388a"
		 "4bd7c8c9aef6d894abc1e0aa8255635f"
		 "dc0c5bbff49c721c71f2f93238d011a4"
		 "4af8f0249b5c1cb099d5c2a4e3c31128"
		 #"75d5aa3c7fa8945004550bb11996706e"
		 #"c4930c9adba0486e77b83510cd4ac977"
		 #"39981f6964084a639094d71512c2141d"
		 )

prepare() {
	if test -d "${srcdir}/${pkgbase}_v${pkgver}/wave511/code" && test ! -d "${srcdir}/${pkgbase}_v${pkgver}/wave511/wave511"; then
		mv -v "${srcdir}/${pkgbase}_v${pkgver}/wave511/code" "${srcdir}/${pkgbase}_v${pkgver}/wave511/wave511"
	fi
	for patchfile in ${srcdir}/*.patch; do
		patch -g0 -p1 --remove-empty-files --no-backup-if-mismatch --batch --forward --directory="${srcdir}/${pkgbase}_v${pkgver}" --input="${patchfile}"
	done
	# FIXME collect files for omx-il build in here
	mkdir -p "${srcdir}/${pkgbase}_v${pkgver}/target-tmp"
	# gawd why
	sed -i 's/^INSTALL=install/INSTALL=install -D/' "${srcdir}/${pkgbase}_v${pkgver}/omx-il/Makefile"
}

package_codaj12() {
	pkgdesc=""
	# target dir is hard-coded
	ln -s "${pkgdir}" "${srcdir}/${pkgbase}_v${pkgver}/target" 
	cd "${srcdir}/${pkgbase}_v${pkgver}/${pkgname}"
	make -f codaj12_buildroot.mak clean
	make -f codaj12_buildroot.mak
	make -f codaj12_buildroot.mak install
	cp -a -t "${srcdir}/${pkgbase}_v${pkgver}/target-tmp" ${pkgdir}/*
	unlink "${srcdir}/${pkgbase}_v${pkgver}/target"
	# kernel module loading should not touch /root
	rm -r "${pkgdir}/root"
}

package_wave420l() {
	pkgdesc=""
	ln -s "${pkgdir}" "${srcdir}/${pkgbase}_v${pkgver}/target" 
	cd "${srcdir}/${pkgbase}_v${pkgver}/${pkgname}/code"
	make -f WaveEncoder_buildroot.mak clean
	make -f WaveEncoder_buildroot.mak
	make -f WaveEncoder_buildroot.mak install
	cp -a -t "${srcdir}/${pkgbase}_v${pkgver}/target-tmp" ${pkgdir}/*
	unlink "${srcdir}/${pkgbase}_v${pkgver}/target"
	# remove firmware
	rm -r "${pkgdir}/lib"
	# kernel module loading should not touch /root
	rm -r "${pkgdir}/root"
}

package_wave511() {
	pkgdesc=""
	ln -s "${pkgdir}" "${srcdir}/${pkgbase}_v${pkgver}/target"
	cd "${srcdir}/${pkgbase}_v${pkgver}/${pkgname}/${pkgname}"
	make -f WaveDecode_buildroot.mak clean
	make -f WaveDecode_buildroot.mak
	make -f WaveDecode_buildroot.mak install
	cp -a -t "${srcdir}/${pkgbase}_v${pkgver}/target-tmp" ${pkgdir}/*
	unlink "${srcdir}/${pkgbase}_v${pkgver}/target"
	# remove firmware
	rm -r "${pkgdir}/lib"
	# kernel module loading should not touch /root
	rm -r "${pkgdir}/root"
}

# depends on the above hard-coded "target" dir 
package_omx-il() {
	pkgdesc=""
	# move the target-tmp into place
	mv "${srcdir}/${pkgbase}_v${pkgver}/target-tmp" "${srcdir}/${pkgbase}_v${pkgver}/target"
	cd "${srcdir}/${pkgbase}_v${pkgver}/${pkgname}"
	make clean
	make
	# remove target and install into pkgdir
	rm -r "${srcdir}/${pkgbase}_v${pkgver}/target"
	ln -s "${pkgdir}" "${srcdir}/${pkgbase}_v${pkgver}/target"
	make install
	unlink "${srcdir}/${pkgbase}_v${pkgver}/target"
}
