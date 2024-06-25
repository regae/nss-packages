include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=qca-nss-sfe
PKG_RELEASE:=1

PKG_SOURCE_URL:=https://git.codelinaro.org/clo/qsdk/oss/lklm/shortcut-fe.git
PKG_SOURCE_PROTO:=git
PKG_SOURCE_VERSION:=149355f0666a2a9ac09adf2722375086e11c09de
PKG_MIRROR_HASH:=skip
PKG_VERSION:=12.5-$(call version_abbrev,$(PKG_SOURCE_VERSION))

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define KernelPackage/qca-nss-sfe
  SECTION:=kernel
  CATEGORY:=Kernel modules
  SUBMENU:=Network Devices
  TITLE:=Shortcut Forwarding Engine
  DEPENDS:=@TARGET_ipq806x +PACKAGE_kmod-ppp:kmod-ppp +kmod-pppoe
  FILES:=$(PKG_BUILD_DIR)/qca-nss-sfe.ko
endef

define KernelPackage/qca-nss-sfe/description
  This package installs Shortcut Forwarding Engine Module
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/qca-nss-sfe
	$(CP) $(PKG_BUILD_DIR)/exports/* $(1)/usr/include/qca-nss-sfe/
endef

define Build/Configure
	$(LN) exports/sfe_api.h $(PKG_BUILD_DIR)/sfe_api.h
endef

SFE_MAKE_OPTS:= \
	SFE_L2TPV3_SUPPORTED=n \
	SFE_RFS_SUPPORTED=n \
	SFE_PPE_QOS_SUPPORTED=n \
	SFE_PROCESS_LOCAL_OUT=n \
	SFE_BRIDGE_VLAN_FILTERING_ENABLE=n

ifneq ($(CONFIG_IPV6),)
SFE_MAKE_OPTS+=SFE_SUPPORT_IPV6=y
endif

# disable trustsec 0x8909
ifneq ($(CONFIG_SFE_TRUSTSEC_ENABLE),)
SFE_MAKE_OPTS+=SFE_TRUSTSEC_ENABLE=y
endif

define Build/Compile
	+$(MAKE) -C "$(LINUX_DIR)" $(strip $(SFE_MAKE_OPTS)) \
		CROSS_COMPILE="$(TARGET_CROSS)" \
		ARCH="$(LINUX_KARCH)" \
		M="$(PKG_BUILD_DIR)" \
		EXTRA_CFLAGS="$(EXTRA_CFLAGS)" \
		$(KERNEL_MAKE_FLAGS) \
		$(PKG_JOBS) \
		modules
endef

$(eval $(call KernelPackage,qca-nss-sfe))
