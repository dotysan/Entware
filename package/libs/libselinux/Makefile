#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# XXX Entware specific: @SELINUX fake deps

include $(TOPDIR)/rules.mk

PKG_NAME:=libselinux
PKG_VERSION:=3.5
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/SELinuxProject/selinux/releases/download/$(PKG_VERSION)
PKG_HASH:=9a3a3705ac13a2ccca2de6d652b6356fead10f36fb33115c185c5ccdf29eec19

PKG_LICENSE:=libselinux-1.0
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Thomas Petazzoni <thomas.petazzoni@bootlin.com>
PKG_CPE_ID:=cpe:/a:selinuxproject:libselinux

PKG_BUILD_FLAGS:=no-lto

HOST_BUILD_DEPENDS:=libsepol/host musl-fts/host pcre2/host

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/host-build.mk

LIBSELINUX_UTILS := \
	avcstat \
	compute_av \
	compute_create \
	compute_member \
	compute_relabel \
	getconlist \
	getdefaultcon \
	getenforce \
	getfilecon \
	getpidcon \
	getsebool \
	getseuser \
	matchpathcon \
	policyvers \
	sefcontext_compile \
	selabel_digest \
	selabel_get_digests_all_partial_matches \
	selabel_lookup \
	selabel_lookup_best_match \
	selabel_partial_match \
	selinux_check_access \
	selinux_check_securetty_context \
	selinuxenabled \
	selinuxexeccon \
	setenforce \
	setfilecon \
	togglesebool \
	validatetrans

LIBSELINUX_ALTS := \
	getenforce \
	getsebool \
	matchpathcon \
	selinuxenabled \
	setenforce

$(eval $(foreach a,$(LIBSELINUX_ALTS),ALTS_$(a):=300:/opt/sbin/$(a):/opt/sbin/libselinux-$(a)$(newline)))

define Package/libselinux/Default
  TITLE:=Runtime SELinux library
  URL:=http://selinuxproject.org/page/Main_Page
endef

define Package/libselinux
  $(call Package/libselinux/Default)
  SECTION:=libs
  CATEGORY:=Libraries
  DEPENDS:=+libsepol +libpcre2 +USE_MUSL:musl-fts @SELINUX
endef

define Package/libselinux/description
	libselinux is the runtime SELinux library that provides
	interfaces (e.g. library functions for the SELinux kernel
	APIs like getcon(), other support functions like
	getseuserbyname()) to SELinux-aware applications. libselinux
	may use the shared libsepol to manipulate the binary policy
	if necessary (e.g. to downgrade the policy format to an
	older version supported by the kernel) when loading policy.
endef

define GenUtilPkg
 define Package/$(1)
   $(call Package/libselinux/Default)
   TITLE+= $(2) utility
   SECTION:=utils
   DEPENDS:=+libselinux
   CATEGORY:=Utilities
   SUBMENU:=libselinux tools
   ALTERNATIVES:=$(ALTS_$(2))
 endef

 define Package/$(1)/description
  libselinux version of the $(2) utility.
 endef
endef

$(foreach a,$(LIBSELINUX_UTILS),$(eval $(call GenUtilPkg,libselinux-$(a),$(a))))

# Needed to link libselinux utilities, which link against
# libselinux.so, which indirectly depends on libpcre.so, installed in
# $(STAGING_DIR_HOSTPKG).
HOST_LDFLAGS += -Wl,-rpath="$(STAGING_DIR_HOSTPKG)/lib"

HOST_MAKE_FLAGS += \
	PREFIX=$(STAGING_DIR_HOSTPKG) \
	SHLIBDIR=$(STAGING_DIR_HOSTPKG)/lib \
	FTS_LDLIBS=-lfts

ifeq ($(CONFIG_USE_MUSL),y)
MAKE_FLAGS += FTS_LDLIBS=-lfts
TARGET_CFLAGS += -D_LARGEFILE64_SOURCE
endif

MAKE_FLAGS += \
	PREFIX=/opt \
	SHLIBDIR=/opt/lib \
	OS=Linux

define Build/Compile
	$(call Build/Compile/Default,all)
endef

define Build/Install
	$(call Build/Install/Default,install)
endef

# XXX Entware specific: don`t install dev
#define Build/InstallDev
#	$(INSTALL_DIR) $(1)/opt/include
#	$(CP) $(PKG_INSTALL_DIR)/opt/include/* $(1)/opt/include/
#	$(INSTALL_DIR) $(1)/opt/lib
#	$(CP) $(PKG_INSTALL_DIR)/opt/lib/* $(1)/opt/lib/
#	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
#	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/opt/lib/pkgconfig/libselinux.pc $(1)/opt/lib/pkgconfig/
#	$(SED) 's,/opt/include,$$$${prefix}/include,g' $(1)/opt/lib/pkgconfig/libselinux.pc
#	$(SED) 's,/opt/lib,$$$${exec_prefix}/lib,g' $(1)/opt/lib/pkgconfig/libselinux.pc
#endef

define Package/libselinux/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libselinux.so.* $(1)/opt/lib/
endef

define BuildUtil
  define Package/$(1)/install
	$(INSTALL_DIR) $$(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/$(2) $$(1)/opt/sbin/$(if $(ALTS_$(2)),libselinux-$(2),$(2))
  endef

  $$(eval $$(call BuildPackage,$(1)))
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,libselinux))
$(foreach a,$(LIBSELINUX_UTILS),$(eval $(call BuildUtil,libselinux-$(a),$(a))))
