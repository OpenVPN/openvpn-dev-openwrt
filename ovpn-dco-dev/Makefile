# SPDX-License-Identifier: GPL-2.0-only

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=ovpn-dco-dev
PKG_RELEASE:=$(AUTORELEASE)

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/OpenVPN/ovpn-dco.git
PKG_BRANCH:=master
PKG_REV:=$(shell git ls-remote $(PKG_SOURCE_URL) $(PKG_BRANCH)|cut -c1-12)
PKG_SOURCE_VERSION:=$(PKG_REV)
PKG_VERSION:=0.git-$(PKG_REV)
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz

PKG_MAINTAINER:=Antonio Quartulli <antonio@openvpn.net>
PKG_LICENSE:=GPL-2.0-only
PKG_LICENSE_FILES:=LICENSES/preferred/GPL-2.0

include $(INCLUDE_DIR)/package.mk

define KernelPackage/ovpn-dco-dev
  SUBMENU:=Network Support
  URL:=https://www.openvpn.net
  TITLE:=OpenVPN Data Channel Offload (development package)
  DEPENDS:=+kmod-crypto-aead +kmod-udptunnel4 +kmod-udptunnel6
  FILES:=$(PKG_BUILD_DIR)/drivers/net/ovpn-dco/ovpn-dco.$(LINUX_KMOD_SUFFIX)
  AUTOLOAD:=$(call AutoLoad,30,ovpn-dco)
endef

define KernelPackage/ovpn-dco/description
  This is the OpenVPN DCO kernel module. It is used to offload the data channel
  to kernelspace and improve your VPN performance.
endef

define KernelPackage/ovpn-dco-dev/config
  if PACKAGE_kmod-ovpn-dco-dev

	config OVPN_DCO_DEBUG
		bool "Enable debugging"
		default n
		help
		  To be used for development and debugging, enabled additional, but
		  noisy, debugging messages.
  endif
endef

PKG_EXTRA_KCONFIG:= CONFIG_OVPN_DCO=m
PKG_EXTRA_CFLAGS:= $(if $(CONFIG_OVPN_DCO_DEBUG),-DDEBUG=1,)
PKG_EXTMOD_SUBDIRS = drivers/net/ovpn-dco

NOSTDINC_FLAGS = \
	$(KERNEL_NOSTDINC_FLAGS) \
	-I$(PKG_BUILD_DIR)/drivers/net/ovpn-dco \
	-I$(PKG_BUILD_DIR)/include \
	-include $(PKG_BUILD_DIR)/linux-compat.h \
	-DOVPN_DCO_VERSION=\\\"$(PKG_VERSION)-openwrt\\\"

define Build/Compile
	$(MAKE) $(PKG_JOBS) -C "$(LINUX_DIR)" \
		$(KERNEL_MAKE_FLAGS) \
		M="$(PKG_BUILD_DIR)/drivers/net/ovpn-dco" \
		$(PKG_EXTRA_KCONFIG) \
		EXTRA_CFLAGS="$(PKG_EXTRA_CFLAGS)" \
		NOSTDINC_FLAGS="$(NOSTDINC_FLAGS)" \
		modules
endef

$(eval $(call KernelPackage,ovpn-dco-dev))
