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

#
# Copyright (c) 2011, 2017, Oracle and/or its affiliates. All rights reserved.
#
BUILD_BITS= 64
COMPILER= gcc
include ../../make-rules/shared-macros.mk

COMPONENT_NAME=		doxygen
COMPONENT_VERSION=	1.8.13
COMPONENT_PROJECT_URL=	http://www.stack.nl/~dimitri/doxygen/index.html
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).src.tar.gz
COMPONENT_ARCHIVE_HASH=	\
    sha256:af667887bd7a87dc0dbf9ac8d86c96b552dfb8ca9c790ed1cbffaa6131573f6b
COMPONENT_ARCHIVE_URL=	http://ftp.stack.nl/pub/users/dimitri/$(COMPONENT_ARCHIVE)
COMPONENT_ANITYA_ID=	457

# Download the manual so that we don't have to keep updating it in the gate.
COMPONENT_ARCHIVE_1 =	$(COMPONENT_NAME)_manual-$(COMPONENT_VERSION).pdf.zip
COMPONENT_ARCHIVE_HASH_1 =	\
    sha256:efc4937cf13b2e74f50ad8806d9b900f3c5e3f987ed503d763180053ee677ecd
COMPONENT_ARCHIVE_URL_1 =	ftp://ftp.stack.nl/pub/users/dimitri/$(COMPONENT_ARCHIVE_1)

TPNO=			34098

BUILD_STYLE= cmake
SYSTEM_TEST_TARGET= $(SYSTEM_TEST_64)
include $(WS_MAKE_RULES)/common.mk

ifeq   ($(strip $(PARFAIT_BUILD)),yes)
PATH=$(PARFAIT_TOOLS):$(GCC_ROOT)/bin:$(USRBINDIR)
else
# Necessary to find the right gcc during build
PATH=$(GCC_ROOT)/bin:$(USRBINDIR)
endif

# This test fails because we don't have bibtext. Unfortunately the testing
# stops there and does not continue. As a workaround we remove this test.
COMPONENT_POST_UNPACK_ACTION += ( cd $(@D); $(RM) testing/012_cite.dox )

# Missing files in build dir for configure without this.
COMPONENT_PRE_CONFIGURE_ACTION =	($(CLONEY) $(SOURCE_DIR) $(@D))

CONFIGURE_OPTIONS +=	--platform solaris-g++
CONFIGURE_OPTIONS +=	--enable-langs=am,ar,br,ca,cn,cz,de,dk,en,eo,es,fa,fi,fr,gr,hr,hu,id,it,je,jp,ke,kr,lt,lv,mk,nl,no,pl,pt,ro,ru,sc,si,sk,sr,sv,tr,tw,ua,vi,za

# Tests not available from the top-level Makefile, nor via a standard target.
# We need to add /usr/gnu/bin to the front of the PATH environment variable
# for "diff -u" and we need to use our latest doxygen binary, not the one
# installed on the system.
#
# Test 12 is a known failure (we don't have bibtex in Solaris).
COMPONENT_TEST_ENV =		PATH="$(GNUBIN):$(PROTO_DIR)/usr/bin:$(PATH)"
COMPONENT_TEST_DIR =		$(BUILD_DIR_64)/testing
COMPONENT_TEST_TARGETS =	tests

COMPONENT_TEST_MASTER = \
	$(COMPONENT_TEST_RESULTS_DIR)/results-all.master

# The additional doxygen specific set of transforms to be applied to the
# test results to try to normalize them.
COMPONENT_TEST_TRANSFORMS += \
	'-e "/^ok/d" ' \

COMPONENT_SYSTEM_TEST_ENV =	PATH="$(GNUBIN):$(PATH)"
COMPONENT_SYSTEM_TEST_DIR =	$(SOURCE_DIR)/testing
COMPONENT_SYSTEM_TEST_CMD =	perl
COMPONENT_SYSTEM_TEST_ARGS =	runtests.pl --doxygen /usr/bin/doxygen
COMPONENT_SYSTEM_TEST_TARGETS =

COMPONENT_POST_INSTALL_ACTION += \
	cd $(PROTO_DIR) ; $(UNPACK) ../../../$(COMPONENT_ARCHIVE_1)

REQUIRED_PACKAGES += developer/lexer/flex
REQUIRED_PACKAGES += developer/parser/bison
REQUIRED_PACKAGES += system/library/gcc/gcc-c++-runtime
REQUIRED_PACKAGES += system/library/gcc/gcc-c-runtime
REQUIRED_PACKAGES += system/library/math
