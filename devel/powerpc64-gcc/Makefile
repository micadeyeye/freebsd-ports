# $FreeBSD$

PORTNAME=	gcc
PORTVERSION=	6.3.0
CATEGORIES=	devel
MASTER_SITES=	GCC/releases/gcc-${DISTVERSION}
PKGNAMEPREFIX?=	powerpc64-

MAINTAINER=	kan@FreeBSD.org
COMMENT=	Cross GNU Compiler Collection for ${PKGNAMEPREFIX:C/-//g}

LICENSE=	GPLv3 GPLv3RLE
LICENSE_COMB=	multi

LIB_DEPENDS=	libgmp.so:math/gmp \
		libmpfr.so:math/mpfr \
		libmpc.so:math/mpc
BUILD_DEPENDS=	${BU_PREFIX}-as:devel/${PKGNAMEPREFIX}binutils
RUN_DEPENDS=	${BU_PREFIX}-as:devel/${PKGNAMEPREFIX}binutils

CONFIGURE_ENV=	gcc_gxx_include_dir="/usr/include/c++/v1"

USES=	gmake iconv libtool tar:bzip2 makeinfo compiler
PLIST_SUB=	TARGETARCH=${PKGNAMEPREFIX:C/-//g} \
		OPSYS=${OPSYS:tl} \
		GCC_TARGET=${GCC_TARGET} \
		GCC_VERSION=${PORTVERSION}

.if empty(GCC_TARGET)
# We are building for a FreeBSD target
GCC_TARGET?=	${PKGNAMEPREFIX}unknown-${OPSYS:tl}${OSREL}
BU_PREFIX?=	${PKGNAMEPREFIX}unknown-${OPSYS:tl}${OSREL}
EXTRA_PATCHES+= ${FILESDIR}/freebsd-format-extensions
.else
BU_PREFIX?=	${GCC_TARGET}
.if defined(FREEBSD_EXTENSION)
EXTRA_PATCHES+= ${FILESDIR}/freebsd-format-extensions
.endif
.endif

TARGETARCH=${PKGNAMEPREFIX:C/-//g}

GNU_CONFIGURE=	yes
CONFIGURE_OUTSOURCE=	yes
CONFIGURE_ARGS+=--target=${GCC_TARGET} --disable-nls --enable-languages=c,c++ \
		--without-headers \
		--with-gmp=${LOCALBASE} \
		--with-pkgversion="FreeBSD Ports Collection for ${PKGNAMEPREFIX:C/-//g}" \
		--with-system-zlib \
		--with-gcc-include-dir=/usr/include/c++/v1/ \
		--with-as=${LOCALBASE}/bin/${BU_PREFIX}-as \
		--with-ld=${LOCALBASE}/bin/${BU_PREFIX}-ld

ALL_TARGET?=	all-gcc
INSTALL_TARGET?=	install-gcc

.include <bsd.port.options.mk>
.include <bsd.port.pre.mk>

post-patch:
	@${REINPLACE_CMD} -e '/LIBSTDCXX/ s/stdc\+\+/c\+\+/g ' \
		${WRKSRC}//gcc/cp/g++spec.c

.if ${TARGETARCH} == "amd64"
CONFIGURE_TARGET=	x86_64-unknown-${OPSYS:tl}${OSREL}
.else
CONFIGURE_TARGET=	${ARCH}-unknown-${OPSYS:tl}${OSREL}
.endif

.if ${TARGETARCH} == "armv6" || ${TARGETARCH} == "aarch64"
. if ${COMPILER_TYPE} == clang
MAKE_ARGS+=CXXFLAGS=-fbracket-depth=512
. endif
.endif

post-install:
.for f in cpp gcov gcov-tool
	@if [ -f ${STAGEDIR}${PREFIX}/bin/$f ] ; then \
		${MV} ${STAGEDIR}${PREFIX}/bin/$f  \
		    ${STAGEDIR}${PREFIX}/bin/${GCC_TARGET}-$f ; \
	fi
.endfor
.for f in c++ cpp g++ gcc gcc-ar gcc-nm gcc-ranlib gcov gcov-tool
	@if [ ! -f ${STAGEDIR}${PREFIX}/man/man1/${GCC_TARGET}-$f.1 ] ; then \
		if [ -f ${STAGEDIR}${PREFIX}/man/man1/$f.1 ] ; then \
			${CP} ${STAGEDIR}${PREFIX}/man/man1/$f.1  \
			    ${STAGEDIR}${PREFIX}/man/man1/${GCC_TARGET}-$f.1 ; \
		fi \
	fi
	@${RM} ${STAGEDIR}${PREFIX}/bin/$f
	@${RM} ${STAGEDIR}${PREFIX}/man/man1/$f.1
	@${RM} ${STAGEDIR}${PREFIX}/man/man1/$f.1.gz
.endfor
	@${RM} ${STAGEDIR}${PREFIX}/info/*
	@${RM} ${STAGEDIR}${PREFIX}/man/man7/*

# This port and all its slave ports fail on aarch64 -- but not all
# the same way.  Provide this definition for the master.
BROKEN_aarch64?=	fails to package

.include <bsd.port.post.mk>
