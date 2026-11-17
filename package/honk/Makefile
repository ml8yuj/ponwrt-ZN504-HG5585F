#
# OpenWrt package: honk (prebuilt official musl static binary with embedded UI)
#

# $(CURDIR) points at the buildroot root, and $(MAKEFILE_LIST) grows with every
# included file, so the package directory has to be resolved before the first
# include.
PKG_DIR:=$(patsubst %/,%,$(dir $(abspath $(lastword $(MAKEFILE_LIST)))))

include $(TOPDIR)/rules.mk

PKG_NAME:=honk
# OpenWrt APK v3 prohibits hyphens in PKG_VERSION. Use underscore for pre-release suffixes.
PKG_VERSION:=0.1.0_beta6
PKG_RELEASE:=1

HONK_GIT_TAG:=v0.1.0-beta.6

ifeq ($(ARCH),aarch64)
	HONK_ARCH:=aarch64-unknown-linux-musl
	HONK_HASH:=5e4c0359950e0f19fac53e31ef9ffbe9f0aafca92331c3fa81e5b56212950a6e
endif
ifeq ($(ARCH),x86_64)
	HONK_ARCH:=x86_64-unknown-linux-musl
	HONK_HASH:=2783b8f0856fc1aecd1cd1d911d9a904c593cd507cf61e5fa24fbd7075317244
endif

PKG_SOURCE:=honk-core-$(HONK_GIT_TAG)-$(HONK_ARCH)-full.tar.gz
PKG_SOURCE_URL:=https://github.com/daeuniverse/honk/releases/download/$(HONK_GIT_TAG)/
PKG_HASH:=$(HONK_HASH)

PKG_LICENSE:=GPL-3.0-only
PKG_LICENSE_FILES:=LICENSE
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk

define Package/honk
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Web Servers/Proxies
  TITLE:=eBPF transparent proxy engine (honk-core) with embedded UI
  URL:=https://github.com/daeuniverse/honk
  LICENSE:=GPL-3.0-only
  DEPENDS:=@(aarch64||x86_64) +kmod-veth +kmod-sched-core +kmod-sched-bpf \
           +kmod-nft-core +kmod-nft-queue +kmod-nfnetlink-queue \
           +kmod-nf-conntrack +bpftool +ip-full +v2ray-geoip +v2ray-geosite
endef

define Package/honk/description
 honk is an eBPF transparent proxy engine with embedded doona dashboard.
endef

define Package/honk/conffiles
/etc/config/honk
/etc/honk/config.dae
endef

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
	tar -xzf $(DL_DIR)/$(PKG_SOURCE) -C $(PKG_BUILD_DIR)
	find $(PKG_BUILD_DIR) -name honk-core -type f -exec cp -f {} $(PKG_BUILD_DIR)/honk-core \;
endef

define Build/Compile
endef

define Package/honk/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/honk-core $(1)/usr/bin/honk-core
	$(LN) honk-core $(1)/usr/bin/honk
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) $(PKG_DIR)/files/honk.init $(1)/etc/init.d/honk
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) $(PKG_DIR)/files/honk.config $(1)/etc/config/honk
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_BIN) $(PKG_DIR)/files/honk.uci-defaults $(1)/etc/uci-defaults/90-honk-autostart
	$(INSTALL_DIR) $(1)/etc/honk
	$(INSTALL_CONF) $(PKG_DIR)/files/config.dae $(1)/etc/honk/config.dae
	$(INSTALL_DIR) $(1)/lib/upgrade/keep.d
	printf '%s\n' '/etc/honk/data/' > $(1)/lib/upgrade/keep.d/honk
	$(INSTALL_DIR) $(1)/usr/share/honk
	$(LN) ../v2ray/geoip.dat $(1)/usr/share/honk/geoip.dat
	$(LN) ../v2ray/geosite.dat $(1)/usr/share/honk/geosite.dat
endef

$(eval $(call BuildPackage,honk))
