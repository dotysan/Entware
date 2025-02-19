#
# Copyright (C) 2017 Shane Peelar
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=pcre2
PKG_VERSION:=10.42
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=https://github.com/PCRE2Project/pcre2/releases/download/$(PKG_NAME)-$(PKG_VERSION)
PKG_HASH:=8d36cd8cb6ea2a4c2bb358ff6411b0c788633a2a45dabbf1aeb4b701d1b5e840

PKG_MAINTAINER:=Shane Peelar <lookatyouhacker@gmail.com>
PKG_LICENSE:=BSD-3-Clause
PKG_LICENSE_FILES:=LICENCE
PKG_CPE_ID:=cpe:/a:pcre:pcre

PKG_CONFIG_DEPENDS:=\
	CONFIG_PACKAGE_libpcre2-16 \
	CONFIG_PACKAGE_libpcre2-32 \
	CONFIG_PCRE2_JIT_ENABLED

PKG_BUILD_DEPENDS:=zlib

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/libpcre2/default
  SECTION:=libs
  CATEGORY:=Libraries
  URL:=https://www.pcre.org/
endef

define Package/libpcre2/config
  source "$(SOURCE)/Config.in"
endef

define Package/libpcre2
  $(call Package/libpcre2/default)
  TITLE:=A Perl Compatible Regular Expression library
endef

define Package/libpcre2-16
  $(call Package/libpcre2/default)
  TITLE:=A Perl Compatible Regular Expression library (16bit support)
endef

define Package/libpcre2-32
  $(call Package/libpcre2/default)
  TITLE:=A Perl Compatible Regular Expression library (32bit support)
endef

CMAKE_HOST_OPTIONS += \
	-DBUILD_SHARED_LIBS=OFF \
	-DPCRE2_BUILD_PCRE2_8=ON \
	-DPCRE2_BUILD_PCRE2_16=ON \
	-DPCRE2_BUILD_PCRE2_32=ON \
	-DPCRE2_DEBUG=OFF \
	-DPCRE2_DISABLE_PERCENT_ZT=ON \
	-DPCRE2_SUPPORT_JIT=OFF \
	-DPCRE2_SHOW_REPORT=OFF \
	-DPCRE2_BUILD_PCRE2GREP=OFF \
	-DPCRE2_BUILD_TESTS=OFF

CMAKE_OPTIONS += \
	-DBUILD_SHARED_LIBS=ON \
	-DPCRE2_BUILD_PCRE2_8=ON \
	-DPCRE2_BUILD_PCRE2_16=O$(if $(CONFIG_PACKAGE_libpcre2-16),N,FF) \
	-DPCRE2_BUILD_PCRE2_32=O$(if $(CONFIG_PACKAGE_libpcre2-32),N,FF) \
	-DPCRE2_DEBUG=OFF \
	-DPCRE2_DISABLE_PERCENT_ZT=ON \
	-DPCRE2_SUPPORT_JIT=O$(if $(CONFIG_PCRE2_JIT_ENABLED),N,FF) \
	-DPCRE2_SHOW_REPORT=OFF \
	-DPCRE2_BUILD_PCRE2GREP=OFF \
	-DPCRE2_BUILD_TESTS=OFF

define Build/InstallDev
	$(call Build/InstallDev/cmake,$(1))
	$(SED) 	's,^\(prefix\|exec_prefix\)=.*,\1=$(STAGING_DIR)/opt,g' $(1)/opt/bin/pcre2-config
	$(INSTALL_DIR) $(2)/bin
	$(LN) ../../opt/bin/pcre2-config $(2)/bin/pcre2-config
endef

define Package/libpcre2/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libpcre2-{8,posix}.so* $(1)/opt/lib/
endef

define Package/libpcre2-16/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libpcre2-16.so* $(1)/opt/lib/
endef

define Package/libpcre2-32/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libpcre2-32.so* $(1)/opt/lib/
endef

$(eval $(call BuildPackage,libpcre2))
$(eval $(call BuildPackage,libpcre2-16))
$(eval $(call BuildPackage,libpcre2-32))
$(eval $(call HostBuild))
