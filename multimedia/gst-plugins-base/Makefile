#
# Copyright (C) 2008-2013 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=gst-plugins-base
PKG_VERSION:=1.2.1
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=http://gstreamer.freedesktop.org/src/gst-plugins-base/
PKG_MD5SUM:=2e75fb03ed7e4d24d88601fcac4d57aa

PKG_BUILD_DEPENDS:= gstreamer liboil
PKG_CONFIG_DEPENDS:= \
	CONFIG_PACKAGE_gst-mod-alsa \
	CONFIG_PACKAGE_gst-mod-app \
	CONFIG_PACKAGE_gst-mod-audioconvert \
	CONFIG_PACKAGE_gst-mod-audiorate \
	CONFIG_PACKAGE_gst-mod-audioresample \
	CONFIG_PACKAGE_gst-mod-audiotestsrc \
	CONFIG_PACKAGE_gst-mod-gio \
	CONFIG_PACKAGE_gst-mod-ogg \
	CONFIG_PACKAGE_gst-mod-tcp \
	CONFIG_PACKAGE_gst-mod-theora \
	CONFIG_PACKAGE_gst-mod-videotestsrc \
	CONFIG_PACKAGE_gst-mod-volume \
	CONFIG_PACKAGE_gst-mod-vorbis \

HOST_BUILD_DEPENDS:=gstreamer/host liboil/host gobject-introspection/host

PKG_FIXUP:=autoreconf
HOST_FIXUP:=autoreconf
PKG_INSTALL:=1

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

# -liconv crept in from pkg-config, to be revisited later
include $(INCLUDE_DIR)/nls.mk

define Package/gstreamer/Default
  CATEGORY:=Multimedia
  SECTION:=multimedia
  TITLE:=GStreamer
  URL:=http://gstreamer.freedesktop.org/
endef

define Package/gstreamer/description/Default
 GStreamer open source multimedia framework
endef


define Package/gst-plugins-base
$(call Package/gstreamer/Default)
  TITLE+= plugins collection (base)
  DEPENDS+= $(GST_DEPENDS)
endef

define Package/gst-plugins-base/description
$(call Package/gstreamer/description/Default)
 .
 This meta package contains only dependencies to the other libraries and
 plugins from the base plugins collection.
endef


GST_COND_SELECT = $(patsubst %,$(if $(CONFIG_PACKAGE_gst-mod-$(1)),--enable,--disable)-%,$(1))

GST_VERSION:=1.0

CONFIGURE_ARGS += \
	--disable-examples \
	\
	$(call GST_COND_SELECT,alsa) \
	--disable-alsa-test \
	$(call GST_COND_SELECT,app) \
	$(call GST_COND_SELECT,audioconvert) \
	$(call GST_COND_SELECT,audiorate) \
	$(call GST_COND_SELECT,audioresample) \
	$(call GST_COND_SELECT,audiotestsrc) \
	--disable-cdparanoia \
	--disable-ffmpegcolorspace \
	--disable-freetypetest \
	$(call GST_COND_SELECT,gio) \
	--disable-gnome_vfs \
	--disable-gst_v4l \
	--disable-libvisual \
	$(call GST_COND_SELECT,ogg) \
	--disable-oggtest \
	--disable-pango \
	--disable-subparse \
	$(call GST_COND_SELECT,tcp) \
	$(call GST_COND_SELECT,theora) \
	--disable-videorate \
	--disable-videoscale \
	$(call GST_COND_SELECT,videotestsrc) \
	$(call GST_COND_SELECT,volume) \
	$(call GST_COND_SELECT,vorbis) \
	--disable-vorbistest \
	--disable-x \
	--disable-xshm \
	--disable-xvideo \
	\
	--without-libiconv-prefix \
	--without-libintl-prefix \
	--without-x \
	\
	--with-audioresample-format=int \

EXTRA_LDFLAGS+= \
	-Wl,-rpath-link=$(STAGING_DIR)/usr/lib \


define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/gstreamer-$(GST_VERSION)
	( cd $(PKG_INSTALL_DIR); $(CP) \
		./usr/include/gstreamer-$(GST_VERSION)/* \
		$(1)/usr/include/gstreamer-$(GST_VERSION)/ \
	)
	$(INSTALL_DIR) $(1)/usr/lib
	( cd $(PKG_INSTALL_DIR); $(CP) \
		./usr/lib/libgst*-$(GST_VERSION).{la,so*} \
		$(1)/usr/lib/ \
	)
	$(INSTALL_DIR) $(1)/usr/lib/gstreamer-$(GST_VERSION)
	( cd $(PKG_INSTALL_DIR); $(CP) \
		./usr/lib/gstreamer-$(GST_VERSION)/libgst*.{la,so} \
		$(1)/usr/lib/gstreamer-$(GST_VERSION)/ \
	)
	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	( cd $(PKG_INSTALL_DIR); $(CP) \
		./usr/lib/pkgconfig/gstreamer*-$(GST_VERSION).pc \
		$(1)/usr/lib/pkgconfig/ \
	)
endef


define Package/gst-plugins-base/install
	/bin/true
endef


# 1: short name
# 2: description
# 3: dependencies on other gstreamer libraries (short name)
# 4: dependencies on other packages
define GstBuildLibrary

  GST_DEPENDS += +libgst$(1)

  define Package/libgst$(1)
    $(call Package/gstreamer/Default)
    TITLE+= $(2) library (base)
    DEPENDS+= +libgstreamer $$(foreach p,$(3),+libgst$$(p)) $(4)
  endef

  define Package/libgst$(1)/description
   $(call Package/gstreamer/description/Default)
   .
   This package contains the GStreamer $(2) library.
  endef

  define Package/libgst$(1)/install
	$(INSTALL_DIR) $$(1)/usr/lib
	( cd $(PKG_INSTALL_DIR); $(CP) \
		./usr/lib/libgst$(1)-$(GST_VERSION).so.* \
		$$(1)/usr/lib/ \
	)
  endef

  $$(eval $$(call BuildPackage,libgst$(1)))
endef

$(eval $(call GstBuildLibrary,allocators,allocators,,))
$(eval $(call GstBuildLibrary,app,app,,))
$(eval $(call GstBuildLibrary,audio,audio,tag,))
$(eval $(call GstBuildLibrary,fft,FFT,,))
$(eval $(call GstBuildLibrary,pbutils,utils,audio tag video,))
$(eval $(call GstBuildLibrary,riff,RIFF media,audio tag,))
$(eval $(call GstBuildLibrary,rtp,RTP,,))
$(eval $(call GstBuildLibrary,rtsp,RTSP,,))
$(eval $(call GstBuildLibrary,sdp,SDP,,))
$(eval $(call GstBuildLibrary,tag,tag support,,))
$(eval $(call GstBuildLibrary,video,video,,))


# 1: short name
# 2: description
# 3: dependencies on other gstreamer libraries (short name)
# 4: dependencies on other gstreamer plugins (short name)
# 5: dependencies on other packages
define GstBuildPlugin

  GST_DEPENDS += +gst-mod-$(1)

  define Package/gst-mod-$(1)
    $(call Package/gstreamer/Default)
    TITLE+= $(2) plugin (base)
    DEPENDS+= +libgstreamer $$(foreach p,$(3),+libgst$$(p)) $$(foreach p,$(4),+gst-mod-$$(p)) $(5)
  endef

  define Package/gst-mod-$(1)/description
   $(call Package/gstreamer/description/Default)
   .
   This package contains the GStreamer $(2) plugin.
  endef

  define Package/gst-mod-$(1)/install
	$(INSTALL_DIR) $$(1)/usr/lib/gstreamer-$(GST_VERSION)
	( cd $(PKG_INSTALL_DIR); $(CP) \
		./usr/lib/gstreamer-$(GST_VERSION)/libgst$(1).so* \
		$$(1)/usr/lib/gstreamer-$(GST_VERSION)/ \
	)
  endef

  $$(eval $$(call BuildPackage,gst-mod-$(1)))
endef

$(eval $(call GstBuildPlugin,adder,adder,audio tag,,))
$(eval $(call GstBuildPlugin,alsa,ALSA,audio tag,,+alsa-lib))
$(eval $(call GstBuildPlugin,app,app,app,,))
$(eval $(call GstBuildPlugin,audioconvert,audio format conversion,audio tag,,))
$(eval $(call GstBuildPlugin,audiorate,audio rate adjusting,audio tag,,))
$(eval $(call GstBuildPlugin,audioresample,audio resampling,audio tag,,))
$(eval $(call GstBuildPlugin,audiotestsrc,audio test,audio tag,,))
$(eval $(call GstBuildPlugin,encodebin,encodebin,audio pbutils tag video,,))
$(eval $(call GstBuildPlugin,gio,GIO,,,))
$(eval $(call GstBuildPlugin,ogg,Ogg,audio pbutils riff tag video,,+libogg))
$(eval $(call GstBuildPlugin,playback,playback,audio pbutils tag video,,))
$(eval $(call GstBuildPlugin,tcp,TCP,,,))
$(eval $(call GstBuildPlugin,theora,Theora,video tag,ogg,+libtheora))
$(eval $(call GstBuildPlugin,typefindfunctions,'typefind' functions,audio pbutils tag video,,))
$(eval $(call GstBuildPlugin,videoconvert,video format conversion,video,,))
$(eval $(call GstBuildPlugin,videotestsrc,video test,video,,))
$(eval $(call GstBuildPlugin,volume,volume,audio tag,,))
$(eval $(call GstBuildPlugin,vorbis,Vorbis,audio tag,ogg,+libvorbis))

$(eval $(call HostBuild))
$(eval $(call BuildPackage,gst-plugins-base))
