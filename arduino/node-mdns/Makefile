#
# Copyright (C) 2014 Arduino LLC
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NPM_NAME:=mdns
PKG_NAME:=node-$(PKG_NPM_NAME)
PKG_VERSION:=2.2.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NPM_NAME)-$(PKG_VERSION).tgz
PKG_SOURCE_URL:=http://registry.nodejitsu.com/$(PKG_NPM_NAME)/-/
PKG_MD5SUM:=65b93b643cff65667f2517889ba51302

PKG_BUILD_DEPENDS:=node
PKG_NODE_VERSION:=0.10.28

include $(INCLUDE_DIR)/package.mk

define Package/node-mdns
  DEPENDS:=+node
  SUBMENU:=Node.js
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=multicast DNS service discovery
  URL:=https://www.npmjs.org/package/mdns
endef

define Package/node-mdns/description
	multicast DNS service discovery
endef

define Build/Prepare
	/bin/tar xzf $(DL_DIR)/$(PKG_SOURCE) -C $(PKG_BUILD_DIR) --strip-components 1
	$(Build/Patch)
endef

define Build/Compile
	CC="$(TOOLCHAIN_DIR)/bin/mips-openwrt-linux-gcc" \
	CXX="$(TOOLCHAIN_DIR)/bin/mips-openwrt-linux-g++" \
	AR="$(TOOLCHAIN_DIR)/bin/mips-openwrt-linux-ar" \
	RANLIB="$(TOOLCHAIN_DIR)/bin/mips-openwrt-linux-ranlib" \
	LINK="$(TOOLCHAIN_DIR)/bin/mips-openwrt-linux-g++" \
	LD="$(TOOLCHAIN_DIR)/bin/mips-openwrt-linux-g++" \
	PATH="/usr/bin/:$(PATH)" \
	GYPFLAGS="-Dv8_use_mips_abi_hardfloat=false -Dv8_can_use_fpu_instructions=false" \
        CFLAGS="$(TARGET_CFLAGS) $(EXTRA_CFLAGS) $(TARGET_CPPFLAGS) $(EXTRA_CPPFLAGS)" \
        CXXFLAGS="$(TARGET_CXXFLAGS) $(EXTRA_CXXFLAGS) $(TARGET_CPPFLAGS) $(EXTRA_CPPFLAGS)" \
	LDFLAGS="-L$(TOOLCHAIN_DIR)/lib/ -Wl,-rpath-link $(TOOLCHAIN_DIR)/lib/" \
	npm_config_arch=mips \
	npm_config_nodedir=$(BUILD_DIR)/node-v$(PKG_NODE_VERSION)/ \
	PREFIX="$(PKG_INSTALL_DIR)/usr/" \

# I added these lines to fix an error but I have a feeling that it is the wrong
# way to fix the error I was seeing and that it is just installing in the wrong place
    mkdir -p $(PKG_INSTALL_DIR)
    mkdir -p $(PKG_INSTALL_DIR)/usr
    mkdir -p $(PKG_INSTALL_DIR)/usr/bin/

# this dependency is used my node-mdns
    ln -fs libdns_sd.so.1 $(PKG_INSTALL_DIR)/libdns_sd.so
	npm install -g $(PKG_BUILD_DIR)
endef

define Package/node-mdns/install
	mkdir -p $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules $(1)/usr/lib/
	rm -rf $(1)/usr/lib/node_modules/$(PKG_NPM_NAME)/node_modules/ws
endef

$(eval $(call BuildPackage,node-mdns))

