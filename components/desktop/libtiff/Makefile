#
# CDDL HEADER START
#
# The contents of this file are subject to the terms of the
# Common Development and Distribution License (the "License").
# You may not use this file except in compliance with the License.
#
# You can obtain a copy of the license at usr/src/OPENSOLARIS.LICENSE
# or http://www.opensolaris.org/os/licensing.
# See the License for the specific language governing permissions
# and limitations under the License.
#
# When distributing Covered Code, include this CDDL HEADER in each
# file and include the License file at usr/src/OPENSOLARIS.LICENSE.
# If applicable, add the following below this CDDL HEADER, with the
# fields enclosed by brackets "[]" replaced with your own identifying
# information: Portions Copyright [yyyy] [name of copyright owner]
#
# CDDL HEADER END
#
# Copyright (c) 2015, 2016, Oracle and/or its affiliates. All rights reserved.
#

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		libtiff
COMPONENT_VERSION=	4.0.3
COMPONENT_PROJECT_URL=	http://www.remotesensing.org/libtiff/
COMPONENT_SRC=		tiff-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.gz
COMPONENT_ARCHIVE_HASH= \
    sha256:338a4d1d2a43aedb37a9ebef5b00dd3d27ca0d60b7d84b838735f64638896a3e
COMPONENT_ARCHIVE_URL=	http://download.osgeo.org/libtiff/$(COMPONENT_ARCHIVE)
COMPONENT_BUGDB=	library/libtiff

TPNO=			9299

# Added to find the *.3tiff that go into /usr/share/man/man3tiff
PKG_PROTO_DIRS += $(COMPONENT_SRC)/man

include $(WS_MAKE_RULES)/prep.mk
include $(WS_MAKE_RULES)/configure.mk
include $(WS_MAKE_RULES)/ips.mk

CC 			+= $(CC_BITS)

# Only build C library, not C++ library
CONFIGURE_OPTIONS       += --disable-cxx
# Only build shared library, not static libraries
CONFIGURE_OPTIONS       += --enable-shared --disable-static
# Don't build tiffgt utility which drags in a pile of OpenGL dependencies
CONFIGURE_OPTIONS       += ax_cv_check_gl_libgl="no"

COMPONENT_TEST_MASTER = $(COMPONENT_TEST_RESULTS_DIR)/results-all.master
COMPONENT_TEST_ARGS =   -k -i
COMPONENT_TEST_TARGETS	= check
COMPONENT_TEST_TRANSFORMS += \
        '-n ' \
        '-e "/result_count/d" ' \
        '-e "/TOTAL:/p" ' \
        '-e "/SKIP:/p" ' \
        '-e "/PASS:/p" ' \
        '-e "/FAIL:/p" ' \
        '-e "/ERROR:/p" '

ASLR_MODE = $(ASLR_ENABLE)

configure:	$(CONFIGURE_32_and_64)

build:          $(BUILD_32_and_64)

install:        $(INSTALL_32_and_64)

test:           $(TEST_32_and_64)

system-test:    $(SYSTEM_TESTS_NOT_IMPLEMENTED)

REQUIRED_PACKAGES += compress/xz
REQUIRED_PACKAGES += image/library/libjpeg
REQUIRED_PACKAGES += library/zlib
REQUIRED_PACKAGES += system/library
REQUIRED_PACKAGES += system/library/math
