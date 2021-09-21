#
# Copyright (C) 2007-2009 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=open5gs
PKG_VERSION:=2.3.3
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/open5gs/open5gs
PKG_SOURCE_VERSION:=a797578b600993bafa4c525f4064986ecab139b8 #v2.3.3
PKG_LICENSE:=AGPL-3.0
# To autogenerate PKG_MIRROR_HASH:
# First add "PKG_MIRROR_HASH:=skip" to the package Makefile and/or "HASH:=skip", if required.
# make package/open5gs/download V=s
# make package/open5gs/check FIXUP=1 V=s
PKG_MIRROR_HASH:=6b2f93703732d2e39f868585564eb10f15b6377e17886ca39eb54e1089006104

PKG_BUILD_DEPENDS:=meson/host
PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk
include ../../devel/meson/meson.mk

MESON_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)/openwrt-build

define Package/open5gs
	SECTION:=net
	CATEGORY:=Network
	TITLE:=Open5GS, an open source core network for LTE/NR
	URL:=https://open5gs.org/
	MAINTAINER:= Thomas Spellman <thomas@althea.systems>
	DEPENDS:=@IPV6 +libyaml +libtins +libstdcpp +kmod-tun
endef

define Package/open5gs/description
	Open5GS is a C-language Open Source implementation for 5G Core and EPC, i.e. the core network of LTE/NR network.

	This package installs only the user plane portion (UPF and SGWU) of the full suite of tools.
endef

define Build/Configure
	$(call Build/Configure/Meson)
	$(SED) 's,OPEN5GS_VERSION .*,OPEN5GS_VERSION "v$(PKG_VERSION)",' $(MESON_BUILD_DIR)/src/version.h
endef

define Package/open5gs/install
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

$(eval $(call BuildPackage,open5gs))