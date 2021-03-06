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
# Copyright (c) 2015, 2017, Oracle and/or its affiliates. All rights reserved.
#

BUILD_BITS =            64
COMPILER =              gcc
include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		gnome-settings-daemon
COMPONENT_VERSION=	3.18.0
COMPONENT_PROJECT_URL=	http://wiki.gnome.org
COMPONENT_ARCHIVE_HASH= \
	sha256:8d3ef9c18538831ed89122fee0bdaca68b0e482a18b3c4388c4e672aba1b3cd5
COMPONENT_BUGDB=	gnome/session
COMPONENT_ANITYA_ID=	12860

TPNO=			24901

# Needs gobject-introspection, not available on S11.
include $(WS_MAKE_RULES)/no-evaluation.mk

# This component needs X11, so we run vnc session, run the tests inside and
# tear down vnc
COMPONENT_PRE_TEST_ACTION += \
	( \
		set -x; \
		cd $(@D); \
		: ; \
		: We do not want to cripple user\'s home, instead create special directory for it; \
		: ; \
		mkdir -p myhome/.vnc; \
		: ; \
		: Simple xterm will do, do not run whole gnome; \
		: ; \
		echo xterm > myhome/.vnc/xstartup; \
		chmod +x myhome/.vnc/xstartup; \
		export HOME=$(@D)/myhome; \
		: ; \
		: Create passwd file. If you want to connect to the VNC do this:; \
		:  1 - disable COMPONENT_POST_TEST_ACTION; \
		:  2 - run the tests via "gmake test"; \
		:  3 - copy the amd64/myhome/.vnc/passwd to your machine; \
		:  4 - run vncviewer -passwd passwd ...; \
		: ; \
		/usr/bin/expect -f $(COMPONENT_DIR)/create_vncpasswd_file; \
		vncserver -SecurityTypes VncAuth; \
		: ; \
		: We need to pass the DISPLAY variable somehow to the tests. Do it via file.; \
		: ; \
		vncserver -list | tail -1 | awk '{ print $$1 }' > myhome/DISPLAY; \
	)

COMPONENT_POST_TEST_ACTION += \
	( \
		set -x; \
		cd $(@D); \
		export HOME=$(@D)/myhome; \
		DISPLAY=$$(<myhome/DISPLAY); \
		vncserver -kill $$DISPLAY; \
		rm -rf myhome; \
	)


# We need to read in DISPLAY variable from file and at the same time use it in
# the env(1) command:
COMPONENT_TEST_ENV_CMD = DISPLAY=$$(<$(@D)/myhome/DISPLAY); $(ENV)
COMPONENT_TEST_ENV += DISPLAY="$$DISPLAY"
COMPONENT_TEST_ENV += HOME=$(@D)/myhome
COMPONENT_TEST_ENV += LD_LIBRARY_PATH=/usr/lib/$(MACH64)/libnotify-4

COMPONENT_TEST_TRANSFORMS += \
	'-n ' \
	'-e "/^\# /p" ' \

include $(WS_MAKE_RULES)/gnome-component.mk

# A pkg-config file isn't shipped in Solaris for nss/nspr 64-bit, so we
# workaround this by using the one-off copy in the evolution-data-server
# component directory
PKG_CONFIG_PATHS.64 += $(WS_COMPONENTS)/gnome/evolution-data-server
PKG_CONFIG_PATHS += $(PKG_CONFIG_PATHS.$(BITS))

CONFIGURE_ENV += PATH=$(GNUBIN):$(PATH)

# gudev not yet supported on Solaris.
CONFIGURE_OPTIONS += --disable-gudev
# GNOME NetworkManager not yet supported on Solaris.
CONFIGURE_OPTIONS += --disable-network-manager
# Linux-specific.
CONFIGURE_OPTIONS += --disable-rfkill
CONFIGURE_OPTIONS += --disable-static
# Wayland not yet supported on Solaris.
CONFIGURE_OPTIONS += --disable-wayland
CONFIGURE_OPTIONS += --enable-man
CONFIGURE_OPTIONS += --enable-shared
CONFIGURE_OPTIONS += --with-pic

# Required due to patches.
COMPONENT_PREP_ACTION += (cd $(@D); autoreconf -fiv);

REQUIRED_PACKAGES += desktop/xdg/desktop-file-utils
REQUIRED_PACKAGES += gnome/gsettings-desktop-schemas
REQUIRED_PACKAGES += library/audio/pulseaudio
REQUIRED_PACKAGES += library/desktop/cairo
REQUIRED_PACKAGES += library/desktop/gdk-pixbuf
REQUIRED_PACKAGES += library/desktop/geoclue
REQUIRED_PACKAGES += library/desktop/geocode-glib
REQUIRED_PACKAGES += library/desktop/gobject/gobject-introspection
REQUIRED_PACKAGES += library/desktop/gtk3
REQUIRED_PACKAGES += library/desktop/libnotify
REQUIRED_PACKAGES += library/desktop/libgweather
REQUIRED_PACKAGES += library/desktop/xdg/libcanberra
REQUIRED_PACKAGES += library/glib2
REQUIRED_PACKAGES += library/gnome/gnome-desktop
REQUIRED_PACKAGES += library/lcms2
REQUIRED_PACKAGES += library/nspr
REQUIRED_PACKAGES += library/print/cups-libs
REQUIRED_PACKAGES += library/security/nss
REQUIRED_PACKAGES += service/gnome/desktop-cache
REQUIRED_PACKAGES += service/image/colord
REQUIRED_PACKAGES += shell/expect
REQUIRED_PACKAGES += system/library/fontconfig
REQUIRED_PACKAGES += system/library/math
REQUIRED_PACKAGES += system/library/polkit
REQUIRED_PACKAGES += system/library/upower
REQUIRED_PACKAGES += x11/library/libx11
REQUIRED_PACKAGES += x11/library/libxext
REQUIRED_PACKAGES += x11/library/libxi
REQUIRED_PACKAGES += x11/library/libxtst
REQUIRED_PACKAGES += x11/server/xvnc
