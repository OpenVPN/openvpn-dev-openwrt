# SPDX-License-Identifier: GPL-2.0-only

include $(TOPDIR)/rules.mk

PKG_NAME:=openvpn-dco-dev
PKG_RELEASE:=$(AUTORELEASE)

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/OpenVPN/openvpn.git
PKG_BRANCH:=dco
PKG_REV:=$(shell git ls-remote $(PKG_SOURCE_URL) $(PKG_BRANCH)|cut -c1-12)
PKG_SOURCE_VERSION:=$(PKG_REV)
PKG_VERSION:=2.6.git-$(PKG_REV)
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz

PKG_MAINTAINER:=Antonio Quartulli <antonio@openvpn.net>
PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=LICENSES/preferred/GPL-2.0


PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz

PKG_INSTALL:=1
PKG_FIXUP:=autoreconf
PKG_BUILD_PARALLEL:=1
PKG_CPE_ID:=cpe:/a:openvpn:openvpn

include $(INCLUDE_DIR)/package.mk

define Package/$(PKG_NAME)/Default
  TITLE:=OpenVPN-$(2) with DCO support (development package)
  SECTION:=net
  CATEGORY:=Network
  URL:=http://openvpn.net
  SUBMENU:=VPN
  MENU:=1
  DEPENDS:=+kmod-ovpn-dco-dev +libnl-genl +kmod-tun +OPENVPN_$(1)_ENABLE_LZO:liblzo +OPENVPN_$(1)_ENABLE_IPROUTE2:ip $(3)
  VARIANT:=$(1)
  PROVIDES:=openvpn openvpn-crypto openvpn-dco-dev
endef

Package/$(PKG_NAME)-openssl=$(call Package/$(PKG_NAME)/Default,openssl,OpenSSL,+PACKAGE_$(PKG_NAME)-openssl:libopenssl)
Package/$(PKG_NAME)-mbedtls=$(call Package/$(PKG_NAME)/Default,mbedtls,mbedTLS,+PACKAGE_$(PKG_NAME)-mbedtls:libmbedtls)
Package/$(PKG_NAME)-wolfssl=$(call Package/$(PKG_NAME)/Default,wolfssl,WolfSSL \(experimental\),+PACKAGE_$(PKG_NAME)-wolfssl:libwolfssl)

define Package/$(PKG_NAME)/config/Default
	source "$(SOURCE)/Config-$(1).in"
endef

Package/$(PKG_NAME)-openssl/config=$(call Package/$(PKG_NAME)/config/Default,openssl)
Package/$(PKG_NAME)-mbedtls/config=$(call Package/$(PKG_NAME)/config/Default,mbedtls)
Package/$(PKG_NAME)-wolfssl/config=$(call Package/$(PKG_NAME)/config/Default,wolfssl)

ifeq ($(BUILD_VARIANT),mbedtls)
CONFIG_OPENVPN_MBEDTLS:=y
endif
ifeq ($(BUILD_VARIANT),openssl)
CONFIG_OPENVPN_OPENSSL:=y
endif
ifeq ($(BUILD_VARIANT),wolfssl)
CONFIG_OPENVPN_WOLFSSL:=y
endif

CONFIGURE_VARS += \
	IPROUTE=/sbin/ip \
	NETSTAT=/sbin/netstat

TARGET_CFLAGS += -ffunction-sections -fdata-sections
TARGET_LDFLAGS += -Wl,--gc-sections

define Build/Configure
	$(call Build/Configure/Default, \
		$(if $(CONFIG_OPENVPN_$(BUILD_VARIANT)_ENABLE_SMALL),--enable-small) \
		--enable-dco \
		--disable-selinux \
		--disable-systemd \
		--disable-plugins \
		--disable-debug \
		--disable-pkcs11 \
		$(if $(CONFIG_OPENVPN_$(BUILD_VARIANT)_ENABLE_LZO),--enable,--disable)-lzo \
		$(if $(CONFIG_OPENVPN_$(BUILD_VARIANT)_ENABLE_LZ4),--enable,--disable)-lz4 \
		$(if $(CONFIG_OPENVPN_$(BUILD_VARIANT)_ENABLE_X509_ALT_USERNAME),--enable,--disable)-x509-alt-username \
		$(if $(CONFIG_OPENVPN_$(BUILD_VARIANT)_ENABLE_MANAGEMENT),--enable,--disable)-management \
		$(if $(CONFIG_OPENVPN_$(BUILD_VARIANT)_ENABLE_FRAGMENT),--enable,--disable)-fragment \
		$(if $(CONFIG_OPENVPN_$(BUILD_VARIANT)_ENABLE_MULTIHOME),--enable,--disable)-multihome \
		$(if $(CONFIG_OPENVPN_$(BUILD_VARIANT)_ENABLE_IPROUTE2),--enable,--disable)-iproute2 \
		$(if $(CONFIG_OPENVPN_$(BUILD_VARIANT)_ENABLE_DEF_AUTH),--enable,--disable)-def-auth \
		$(if $(CONFIG_OPENVPN_$(BUILD_VARIANT)_ENABLE_PORT_SHARE),--enable,--disable)-port-share \
		$(if $(CONFIG_OPENVPN_OPENSSL),--with-crypto-library=openssl) \
		$(if $(CONFIG_OPENVPN_MBEDTLS),--with-crypto-library=mbedtls) \
		$(if $(CONFIG_OPENVPN_WOLFSSL),--with-crypto-library=wolfssl) \
	)
endef

define Package/$(PKG_NAME)-$(BUILD_VARIANT)/conffiles
/etc/config/openvpn
/etc/openvpn.user
endef

define Package/$(PKG_NAME)-$(BUILD_VARIANT)/install
	$(INSTALL_DIR) \
		$(1)/usr/sbin \
		$(1)/usr/share/openvpn \
		$(1)/etc/init.d \
		$(1)/etc/config \
		$(1)/etc/openvpn \
		$(1)/lib/functions \
		$(1)/lib/upgrade/keep.d \
		$(1)/usr/libexec \
		$(1)/etc/hotplug.d/openvpn

	$(INSTALL_BIN) \
		$(PKG_INSTALL_DIR)/usr/sbin/openvpn \
		$(1)/usr/sbin/

	$(INSTALL_BIN) \
		files/openvpn.init \
		$(1)/etc/init.d/openvpn

	$(INSTALL_BIN) \
		files/usr/libexec/openvpn-hotplug \
		$(1)/usr/libexec/openvpn-hotplug

	$(INSTALL_DATA) \
		files/lib/functions/openvpn.sh \
		$(1)/lib/functions/openvpn.sh

	$(INSTALL_DATA) \
		files/etc/hotplug.d/openvpn/01-user \
		$(1)/etc/hotplug.d/openvpn/01-user

	$(INSTALL_DATA) \
		files/etc/openvpn.user \
		$(1)/etc/openvpn.user

	$(INSTALL_DATA) \
		files/openvpn.options \
		$(1)/usr/share/openvpn/openvpn.options

	$(INSTALL_CONF) files/openvpn.config \
		$(1)/etc/config/openvpn

	$(INSTALL_DATA) \
		files/openvpn.upgrade \
		$(1)/lib/upgrade/keep.d/openvpn
endef

$(eval $(call BuildPackage,$(PKG_NAME)-openssl))
$(eval $(call BuildPackage,$(PKG_NAME)-mbedtls))
$(eval $(call BuildPackage,$(PKG_NAME)-wolfssl))
