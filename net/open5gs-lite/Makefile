#
# Copyright (C) 2007-2009 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=open5gs-lite
PKG_VERSION:=2.3.3
PKG_RELEASE:=0

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/open5gs/open5gs
PKG_SOURCE_VERSION:=a797578b600993bafa4c525f4064986ecab139b8 #v2.3.3
PKG_LICENSE:=AGPL-3.0
PKG_INSTALL:=1

# To autogenerate PKG_MIRROR_HASH:
# First add "PKG_MIRROR_HASH:=skip" to the package Makefile and/or "HASH:=skip", if required.
# make package/open5gs/download V=s
# make package/open5gs/check FIXUP=1 V=s
PKG_MIRROR_HASH:=7a4f3a44ace338dd467dd3a24d6f90215790f60b6d2697a3bb126df80db4a445

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/meson.mk

define Package/open5gs-lite
	SECTION:=net
	CATEGORY:=Network
	TITLE:=Open5GS, an open source core network for LTE/NR
	URL:=https://open5gs.org/
	MAINTAINER:= Thomas Spellman <thomas@althea.systems>
	DEPENDS:=+libyaml +libtins +libstdcpp +kmod-tun
endef

define Package/open5gs-lite/description
	Open5GS is a C-language Open Source implementation for 5G Core and EPC, i.e. the core of an LTE/NR network.

	This package installs only the user plane portion (UPF and SGWU) of the full suite of tools.
	This set of services run at each tower or backhaul location and communicate with the central server.
endef

define Build/Configure
	$(SED) 's,build_tests =.*,build_tests = false,' $(PKG_BUILD_DIR)/meson.build
	$(SED) "s,subdir('\(mme\|hss\|sgwc\|pcrf\|nrf\|udr\|udm\|pcf\|nssf\|bsf\|ausf\|smf\|amf\)'),," $(PKG_BUILD_DIR)/src/meson.build
	$(SED) 's,#include "ogs-diameter-gx\.h",,' $(PKG_BUILD_DIR)/src/upf/context.h
	$(SED) 's/libdiameter_gx_dep,//' $(PKG_BUILD_DIR)/src/upf/meson.build
	$(SED) "s,subdir('\(sctp\|dbi\|diameter\|asn1c\|ngap\|s1ap\|sbi\)'),," $(PKG_BUILD_DIR)/lib/meson.build
	$(SED) "s,subdir('\(freeDiameter\|systemd\|logrotate\|newsyslog\)'),," $(PKG_BUILD_DIR)/configs/meson.build
	$(call Build/Configure/Meson)
	$(SED) 's,OPEN5GS_VERSION .*,OPEN5GS_VERSION "v$(PKG_VERSION)",' $(MESON_BUILD_DIR)/src/version.h
endef

define Package/open5gs-lite/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/open5gs-upfd $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/open5gs-sgwud $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libogsapp.so* $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libogscore.so* $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libogscrypt.so* $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libogsgtp.so* $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libogsipfw.so* $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libogsnas-common.so* $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libogspfcp.so* $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libogstun.so* $(1)/usr/lib
	$(INSTALL_DIR) $(1)/etc/open5gs
	$(INSTALL_CONF) $(MESON_BUILD_DIR)/configs/open5gs/sgwu.yaml $(1)/etc/open5gs/sgwu.yaml
	$(INSTALL_CONF) $(MESON_BUILD_DIR)/configs/open5gs/upf.yaml $(1)/etc/open5gs/upf.yaml
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/sgwu.init $(1)/etc/init.d/sgwu
	$(INSTALL_BIN) ./files/upf.init $(1)/etc/init.d/upf
endef

$(eval $(call BuildPackage,open5gs-lite))