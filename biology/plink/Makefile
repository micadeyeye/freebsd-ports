# $FreeBSD$

PORTNAME=	plink
PORTVERSION=	1.07
PORTREVISION=	7
CATEGORIES=	biology science
MASTER_SITES=	http://pngu.mgh.harvard.edu/~purcell/plink/dist/
DISTNAME=	${PORTNAME}-${PORTVERSION}-src

MAINTAINER=	jwb@FreeBSD.org
COMMENT=	Whole genome association analysis toolset

BROKEN=		no public distfiles

LICENSE=	GPLv2

LIB_DEPENDS=	liblapack.so:math/lapack

# We need Fortran LDFLAGS to link with Lapack.
USES=		fortran gmake zip

PLIST_FILES=	bin/plink

do-install:
	${MKDIR} ${STAGEDIR}${PREFIX}/bin
	${INSTALL_PROGRAM} ${WRKSRC}/plink ${STAGEDIR}${PREFIX}/bin

.include <bsd.port.mk>
