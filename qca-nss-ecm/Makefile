include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=qca-nss-ecm
PKG_RELEASE:=1

PKG_SOURCE_URL:=https://git.codelinaro.org/clo/qsdk/oss/lklm/qca-nss-ecm.git
PKG_SOURCE_PROTO:=git
PKG_SOURCE_VERSION:=937d47c54cf681381a3bc21fbe74e496c3e9be38
PKG_MIRROR_HASH:=skip
PKG_VERSION:=12.5-$(call version_abbrev,$(PKG_SOURCE_VERSION))

PKG_BUILD_PARALLEL:=1
PKG_CONFIG_DEPENDS := \
	CONFIG_ECM_FRONTEND_SFE \
	CONFIG_ECM_INTERFACE_VXLAN \
	CONFIG_ECM_INTERFACE_OVS_BRIDGE \
	CONFIG_ECM_INTERFACE_OVS_CLASSIFIER \
	CONFIG_ECM_INTERFACE_MACVLAN \
	CONFIG_ECM_INTERFACE_MULTICAST \
	CONFIG_ECM_INTERFACE_MAP_T \
	CONFIG_ECM_INTERFACE_IPSEC \
	CONFIG_ECM_INTERFACE_PPPOE \
	CONFIG_ECM_INTERFACE_L2TPV2 \
	CONFIG_ECM_INTERFACE_GRE \
	CONFIG_ECM_INTERFACE_SIT \
	CONFIG_ECM_INTERFACE_TUNIPIP6 \
	CONFIG_ECM_INTERFACE_NETLINK \
	CONFIG_ECM_INTERFACE_BONDING

include $(INCLUDE_DIR)/package.mk

define KernelPackage/qca-nss-ecm
  TITLE:=QCA NSS Enhanced Connection Manager (ECM)
  SECTION:=kernel
  CATEGORY:=Kernel modules
  SUBMENU:=Network Support
  FILES:=$(PKG_BUILD_DIR)/*.ko $(FILES_EXAMPLES)
  KCONFIG:=CONFIG_BRIDGE_NETFILTER=y \
	CONFIG_NF_CONNTRACK_EVENTS=y \
	CONFIG_NF_CONNTRACK_DSCPREMARK_EXT=y
  DEPENDS:= \
	+kmod-nf-conntrack \
	+PACKAGE_kmod-qca-nss-drv:kmod-qca-nss-drv \
	+ECM_FRONTEND_SFE:kmod-qca-nss-sfe \
	+ECM_INTERFACE_MULTICAST:kmod-qca-mcs \
	+ECM_INTERFACE_BONDING:kmod-bonding \
	+ECM_INTERFACE_VXLAN:kmod-vxlan \
	+ECM_INTERFACE_MACVLAN:kmod-macvlan \
	+ECM_INTERFACE_OVS_BRIDGE:kmod-qca-ovsmgr \
	+ECM_INTERFACE_MAP_T:kmod-nat46 \
	+ECM_INTERFACE_GRE:kmod-gre \
	+ECM_INTERFACE_GRE:kmod-gre6 \
	+ECM_INTERFACE_IPSEC:kmod-ipsec \
	+ECM_INTERFACE_IPSEC:kmod-ipsec4 \
	+ECM_INTERFACE_IPSEC:kmod-ipsec6 \
	+ECM_INTERFACE_PPPOE:kmod-pppoe \
	+ECM_INTERFACE_PPPOE:kmod-pptp \
	+ECM_INTERFACE_PPPOE:kmod-pppol2tp \
	+ECM_INTERFACE_TUNIPIP6:kmod-ip6-tunnel \
	+ECM_INTERFACE_SIT:kmod-sit
endef

define KernelPackage/qca-nss-ecm/Description
This package contains the QCA NSS Enhanced Connection Manager
endef

define KernelPackage/qca-nss-ecm/install
	$(INSTALL_DIR) $(1)/etc/firewall.d $(1)/etc/init.d $(1)/usr/bin $(1)/lib/netifd/offload $(1)/etc/config $(1)/etc/uci-defaults $(1)/etc/sysctl.d $(1)/etc/hotplug.d/net
	$(INSTALL_DATA) ./files/qca-nss-ecm.firewall $(1)/etc/firewall.d/qca-nss-ecm
	$(INSTALL_BIN) ./files/qca-nss-ecm.init $(1)/etc/init.d/qca-nss-ecm
	$(INSTALL_BIN) ./files/ecm_dump.sh $(1)/usr/bin/
	$(INSTALL_BIN) ./files/disable_offloads.sh $(1)/lib/netifd/offload
	$(INSTALL_BIN) ./files/on-demand-down $(1)/lib/netifd/offload/on-demand-down
	$(INSTALL_DATA) ./files/qca-nss-ecm.uci $(1)/etc/config/ecm
	$(INSTALL_DATA) ./files/qca-nss-ecm.defaults $(1)/etc/uci-defaults/99-qca-nss-ecm
	$(INSTALL_BIN) ./files/qca-nss-ecm.sysctl $(1)/etc/sysctl.d/qca-nss-ecm.conf
	$(INSTALL_BIN) ./files/disable_offloads.hotplug $(1)/etc/hotplug.d/net/99-disable_offloads
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/qca-nss-ecm
	$(CP) $(PKG_BUILD_DIR)/exports/* $(1)/usr/include/qca-nss-ecm/
endef

EXTRA_CFLAGS+= \
	-I$(STAGING_DIR)/usr/include/qca-mcs \
	-I$(STAGING_DIR)/usr/include/qca-nss-drv \
	-I$(STAGING_DIR)/usr/include/qca-nss-sfe \
	-I$(STAGING_DIR)/usr/include/nat46

define KernelPackage/qca-nss-ecm/config
menu "Configuration"
	comment "QCA ECM Configs"

	config ECM_FRONTEND_SFE
		bool "Enable SFE Support"
		default n
		select PACKAGE_kmod-qca-nss-sfe

	config ECM_INTERFACE_VXLAN
		bool "VXLAN Support"
		default n

	config ECM_INTERFACE_OVS_BRIDGE
		bool "OVS Bridge Support"
		default n
		select PACKAGE_kmod-qca-ovsmgr

	config ECM_INTERFACE_OVS_CLASSIFIER
		depends on ECM_INTERFACE_OVS_BRIDGE
		bool "OVS Classifier Support"
		default y if ECM_INTERFACE_OVS_BRIDGE

	config ECM_INTERFACE_MACVLAN
		bool "MACVLAN Support"
		default n
		select PACKAGE_kmod-macvlan

	config ECM_INTERFACE_MULTICAST
		bool "Multicast Support"
		default n
		select PACKAGE_kmod-qca-mcs

	config ECM_INTERFACE_MAP_T
		bool "MAP-T Support (nat46)"
		default n
		select PACKAGE_kmod-nat46

	config ECM_INTERFACE_IPSEC
		bool "IPSec Support"
		default n
		select PACKAGE_kmod-ipsec
		select PACKAGE_kmod-ipsec4
		select PACKAGE_kmod-ipsec6

	config ECM_INTERFACE_PPPOE
		bool "PPPOe Support"
		default n
		select PACKAGE_kmod-pppoe
		select NSS_DRV_PPPOE_ENABLE

	config ECM_INTERFACE_L2TPV2
		bool "L2TPv2 Support"
		default n
		select PACKAGE_kmod-pppol2tp

	config ECM_INTERFACE_GRE
		bool "GRE Support"
		default n
		select PACKAGE_kmod-gre
		select PACKAGE_kmod-gre6

	config ECM_INTERFACE_SIT
		bool "SIT Support"
		default n

	config ECM_INTERFACE_TUNIPIP6
		bool "TUNIPIP6 Support"
		default n
		select PACKAGE_kmod-ip6-tunnel

	config ECM_INTERFACE_NETLINK
		bool "Netlink Support"
		default n

	config ECM_INTERFACE_BONDING
		bool "Bonding Support"
		default n
		select PACKAGE_kmod-bonding
endmenu
endef

ifneq (, $(findstring $(CONFIG_TARGET_BOARD), "ipq806x" "qualcommax" "ipq60xx"))
ECM_MAKE_OPTS+=ECM_FRONT_END_NSS_ENABLE=y \
			   ECM_NON_PORTED_SUPPORT_ENABLE=y \
			   ECM_INTERFACE_VLAN_ENABLE=y \
			   ECM_CLASSIFIER_MARK_ENABLE=y \
			   ECM_CLASSIFIER_DSCP_ENABLE=y \
			   ECM_CLASSIFIER_DSCP_IGS=n \
			   ECM_STATE_OUTPUT_ENABLE=y \
			   ECM_DB_ADVANCED_STATS_ENABLE=y \
			   ECM_DB_CONNECTION_CROSS_REFERENCING_ENABLE=y \
			   ECM_TRACKER_DPI_SUPPORT_ENABLE=y \
			   ECM_DB_CLASSIFIER_TYPE_ASSIGNMENTS_TRACK_ENABLE=y \
			   ECM_BAND_STEERING_ENABLE=y \
			   ECM_BRIDGE_VLAN_FILTERING_ENABLE=n \
			   ECM_INTERFACE_OVPN_ENABLE=n \
			   ECM_FRONT_END_PPE_ENABLE=n \
			   ECM_CLASSIFIER_PCC_ENABLE=n
endif

ifneq ($(CONFIG_IPV6),)
ECM_MAKE_OPTS+=ECM_IPV6_ENABLE=y
endif

ifeq ($(CONFIG_ECM_FRONTEND_SFE),y)
ECM_MAKE_OPTS+=ECM_FRONT_END_SFE_ENABLE=y
else
ECM_MAKE_OPTS+=ECM_FRONT_END_SFE_ENABLE=n
endif

ifeq ($(CONFIG_ECM_INTERFACE_VXLAN),y)
ECM_MAKE_OPTS+=ECM_INTERFACE_VXLAN_ENABLE=y
endif

ifeq ($(CONFIG_ECM_INTERFACE_OVS_BRIDGE),y)
ECM_MAKE_OPTS+=ECM_INTERFACE_OVS_BRIDGE_ENABLE=y
EXTRA_CFLAGS+= -I$(STAGING_DIR)/usr/include/qca-ovsmgr
ifeq ($(CONFIG_ECM_INTERFACE_OVS_CLASSIFIER),y)
ECM_MAKE_OPTS+=ECM_CLASSIFIER_OVS_ENABLE=y
endif
endif

ifeq ($(CONFIG_ECM_INTERFACE_MACVLAN),y)
ECM_MAKE_OPTS+=ECM_INTERFACE_MACVLAN_ENABLE=y
endif

ifeq ($(CONFIG_ECM_INTERFACE_MULTICAST),y)
ECM_MAKE_OPTS+=ECM_MULTICAST_ENABLE=y
endif

ifeq ($(CONFIG_ECM_INTERFACE_MAP_T),y)
ECM_MAKE_OPTS+=ECM_INTERFACE_MAP_T_ENABLE=y
endif

ifeq ($(CONFIG_ECM_INTERFACE_IPSEC),y)
ECM_MAKE_OPTS+=ECM_INTERFACE_IPSEC_ENABLE=y
endif

ifeq ($(CONFIG_ECM_INTERFACE_PPPOE),y)
ECM_MAKE_OPTS+=ECM_INTERFACE_PPPOE_ENABLE=y \
			   ECM_INTERFACE_PPTP_ENABLE=y \
			   ECM_INTERFACE_PPP_ENABLE=y
else
ECM_MAKE_OPTS+=ECM_INTERFACE_PPPOE_ENABLE=n \
			   ECM_INTERFACE_PPTP_ENABLE=n \
			   ECM_INTERFACE_PPP_ENABLE=n
endif

ifeq ($(CONFIG_ECM_INTERFACE_L2TPV2),y)
ECM_MAKE_OPTS+=ECM_INTERFACE_L2TPV2_ENABLE=y
else
ECM_MAKE_OPTS+=ECM_INTERFACE_L2TPV2_ENABLE=n
endif

ifeq ($(CONFIG_ECM_INTERFACE_GRE),y)
ECM_MAKE_OPTS+=ECM_INTERFACE_GRE_TAP_ENABLE=y \
			   ECM_INTERFACE_GRE_TUN_ENABLE=y
endif

ifeq ($(CONFIG_ECM_INTERFACE_SIT),y)
ECM_MAKE_OPTS+=ECM_INTERFACE_SIT_ENABLE=y
endif

ifeq ($(CONFIG_ECM_INTERFACE_TUNIPIP6),y)
ECM_MAKE_OPTS+=ECM_INTERFACE_TUNIPIP6_ENABLE=y
endif

ifeq ($(CONFIG_ECM_INTERFACE_NETLINK),y)
ECM_MAKE_OPTS+=ECM_CLASSIFIER_NL_ENABLE=y
endif

ifeq ($(CONFIG_ECM_INTERFACE_BONDING),y)
ECM_MAKE_OPTS+=ECM_INTERFACE_BOND_ENABLE=y
endif

ECM_MAKE_OPTS+=EXAMPLES_BUILD_PCC="n" \
			   EXAMPLES_BUILD_MARK="n" \
			   EXAMPLES_BUILD_OVS="n" \
			   EXAMPLES_BUILD_AE="n"

define Build/Compile
	+$(MAKE) -C "$(LINUX_DIR)" $(strip $(ECM_MAKE_OPTS)) \
		CROSS_COMPILE="$(TARGET_CROSS)" \
		ARCH="$(LINUX_KARCH)" \
		M="$(PKG_BUILD_DIR)" \
		EXTRA_CFLAGS="$(EXTRA_CFLAGS)" SoC="ipq806x" \
		$(KERNEL_MAKE_FLAGS) \
		$(PKG_JOBS) \
		modules
endef

$(eval $(call KernelPackage,qca-nss-ecm))
