---
layout:     post
title:      Wireshark v3.x 安装过程
subtitle:  介绍RHEL/CentOS v7.x 安装Wireshark v3.x详细过程
date:       2019-08-13
author:     HansenWang
header-img: img/post-bg-wireshark.jpg
catalog: true
tags:
    - Linux
    - Wireshark
---

# 前言

本文主要介绍wireshark最新release版3.1.0在RHEL/CentOS v7.x的安装方法, 踩过一些坑，所以记录下。

**Wireshark是一款网络封包捕获与分析的软件**，尽可能捕获所有的网络封包，并解析这些封包的详细信息。使用Libpcap作为接口，与网卡进行数据报文交换。可以用于网络协议分析和调试。

# 准备安装

* 本地或远程YUM源包安装配置
* Python3最新版安装包，官网获取[Python3官网](https://www.python.org/)
* CMake最新源码安装包，官网获取[CMake官网](https://cmake.org/download/)
* libpcap最新的源码安装包，官网获取[TCPDUMP&LiBPCAP官网](https://cmake.org/download/)
* Wireshark最新的源码安装包, 官网获取 [Wireshark官网下载](https://www.wireshark.org/#download)

# 安装步骤

1. 基础包安装
2. Python3安装
3. cmake安装
4. libpcap安装
5. wireshark安装

## 基础包安装

由于我这边的OS选择的是Base安装，所以缺少的包很多，配置好YUM之后可以执行以下命令安装所需要的基础包，部分如果安装就不用安装了

`yum install libpcap org-x11-server-xorg xor-x11-xauth xorg-x11-apps gcc gcc_c++ libstdc++ glib2-devel libgcrypt-devel openssl-devel qt* kernel-devel mesa* libpcap-devel zlib zlib-devel curl curl-devel gnome-desktop3`

> 14:libpcap-1.5.3-11.el7.x86_64	#系统自带的，版本太低，还需要安装最新版本的
c-ares-1.10.0-3.el7.x86_64
libsmi-0.4.8-13.el7.x86_64
freetype-2.8-12.el7.x86_64
libXfixes-5.0.3-1.el7.x86_64
mesa-libglapi-18.0.5-3.el7.x86_64
libdrm-2.4.91-3.el7.x86_64
libXdamage-1.1.4-4.1.el7.x86_64
libxshmfence-1.2-1.el7.x86_64
1:libglvnd-1.0.1-0.8.git5baa1e5.el7.x86_64
hicolor-icon-theme-0.12-7.el7.noarch
libwayland-server-1.15.0-1.el7.x86_64
mesa-libgbm-18.0.5-3.el7.x86_64
libXcursor-1.1.15-1.el7.x86_64
libthai-0.1.14-9.el7.x86_64
graphite2-1.3.10-1.el7_3.x86_64
harfbuzz-1.7.5-2.el7.x86_64
jbigkit-libs-2.0-11.el7.x86_64
libtiff-4.0.3-27.el7_3.x86_64
atk-2.28.1-1.el7.x86_64
pixman-0.34.0-1.el7.x86_64
libXrandr-1.5.1-2.el7.x86_64
fribidi-1.0.2-1.el7.x86_64
libXcomposite-0.4.4-4.1.el7.x86_64
dejavu-fonts-common-2.33-6.el7.noarch
dejavu-sans-fonts-2.33-6.el7.noarch
fontconfig-2.13.0-4.3.el7.x86_64
libXft-2.3.2-2.el7.x86_64
libwayland-client-1.15.0-1.el7.x86_64
1:libglvnd-egl-1.0.1-0.8.git5baa1e5.el7.x86_64
mesa-libEGL-18.0.5-3.el7.x86_64
libXxf86vm-1.1.4-1.el7.x86_64
mesa-libGL-18.0.5-3.el7.x86_64	#mesa 3D图形库，也是图形化界面用到
1:libglvnd-glx-1.0.1-0.8.git5baa1e5.el7.x86_64
cairo-1.15.12-3.el7.x86_64
pango-1.42.4-1.el7.x86_64
jasper-libs-1.900.1-33.el7.x86_64
gdk-pixbuf2-2.36.12-3.el7.x86_64
gtk-update-icon-cache-3.22.30-3.el7.x86_64
libXinerama-1.1.3-2.1.el7.x86_64
gtk2-2.24.31-1.el7.x86_64
wireshark-gnome-1.10.14-16.el7.x86_64	#自己装的时候装了系统自带的版本，后来需要更新到最新的版本
glib2-2.56.1-2.el7.x86_64
json-glib-1.4.2-2.el7.x86_64
1:dbus-libs-1.10.24-12.el7.x86_64
1:dbus-1.10.24-12.el7.x86_64
libarchive-3.1.2-10.el7_2.x86_64
cairo-gobject-1.15.12-3.el7.x86_64
gsettings-desktop-schemas-3.28.0-2.el7.x86_64
libusbx-1.0.21-1.el7.x86_64
libgusb-0.2.9-1.el7.x86_64
at-spi2-core-2.28.0-1.el7.x86_64
at-spi2-atk-2.26.2-1.el7.x86_64
dconf-0.28.0-4.el7.x86_64
libgcab1-0.7-4.el7_4.x86_64
adwaita-gtk2-theme-3.28-2.el7.x86_64
google-noto-emoji-color-fonts-20180508-4.el7.noarch
lcms2-2.6-3.el7.x86_64
colord-libs-1.3.4-1.el7.x86_64
abattis-cantarell-fonts-0.0.25-1.el7.noarch
libwayland-cursor-1.15.0-1.el7.x86_64
libepoxy-1.5.2-1.el7.x86_64
adwaita-cursor-theme-3.28.0-1.el7.noarch
adwaita-icon-theme-3.28.0-1.el7.noarch
gnome-themes-standard-3.28-2.el7.x86_64
xkeyboard-config-2.24-1.el7.noarch
libxkbcommon-0.7.1-1.el7.x86_64
libwayland-egl-1.15.0-1.el7.x86_64
libmodman-2.0.1-8.el7.x86_64
libproxy-0.4.11-11.el7.x86_64
glib-networking-2.56.1-1.el7.x86_64
libsoup-2.62.2-2.el7.x86_64
rest-0.8.1-2.el7.x86_64
gtk3-3.22.30-3.el7.x86_64  #Wireshark也用到gtk图形化用户接口
libappstream-glib-0.7.8-2.el7.x86_64
fuse-2.9.2-11.el7.x86_64
flatpak-libs-1.0.2-2.el7.x86_64
flatpak-1.0.2-2.el7.x86_64
xdg-desktop-portal-1.0.2-1.el7.x86_64
gnome-desktop3-3.28.2-2.el7.x86_64	#Wireshark用到的是GUI，所以得装桌面
libXt-1.1.5-3.el7.x86_64
libXmu-1.1.2-2.el7.x86_64
libxkbfile-1.0.9-3.el7.x86_64
xorg-x11-xkb-utils-7.7-14.el7.x86_64
xorg-x11-server-common-1.20.1-3.el7.x86_64
libXaw-1.0.13-4.el7.x86_64
libXfont2-2.0.3-1.el7.x86_64
libXdmcp-1.1.2-6.el7.x86_64
xorg-x11-server-Xorg-1.20.1-3.el7.x86_64	#我这边是用到了X11-forwarding，所以需要安装以下xorg三个包
xorg-x11-apps-7.7-7.el7.x86_64
1:xorg-x11-xauth-1.0.9-1.el7.x86_64
cmake-2.8.12.2-2.el7.x86_64	#安装了系统自带的cmake，事实证明用于编译最新的工具版本太低
libgcc-4.8.5-36.el7.x86_64
libgomp-4.8.5-36.el7.x86_64
cpp-4.8.5-36.el7.x86_64
gcc-4.8.5-36.el7.x86_64	#源码编译，编译器不能少
libstdc++-4.8.5-36.el7.x86_64
libstdc++-devel-4.8.5-36.el7.x86_64
gcc-c++-4.8.5-36.el7.x86_64 #源码编译，C++编译器也不能少
pcre-devel-8.32-17.el7.x86_64
glib2-devel-2.56.1-2.el7.x86_64  #这玩意是gcc lib库吧，装devel准没错
libgpg-error-devel-1.12-3.el7.x86_64
libgcrypt-devel-1.5.3-14.el7.x86_64  #这玩意儿是个密码库，什么MD5,SHA2,SHA3算法都是可以的
1:openssl-libs-1.0.2k-16.el7.x86_64
1:openssl-1.0.2k-16.el7.x86_64
libcom_err-1.42.9-13.el7.x86_64
krb5-libs-1.15.1-34.el7.x86_64
libkadm5-1.15.1-34.el7.x86_64
libss-1.42.9-13.el7.x86_64
e2fsprogs-libs-1.42.9-13.el7.x86_64
libcom_err-devel-1.42.9-13.el7.x86_64
keyutils-libs-devel-1.5.8-3.el7.x86_64
zlib-1.2.7-18.el7.x86_64
zlib-devel-1.2.7-18.el7.x86_64 #需要用到zlib压缩算法的，主要是python3, 也有可能wireshark处理压缩包
libverto-devel-0.2.5-4.el7.x86_64
libsepol-devel-2.5-10.el7.x86_64
libselinux-devel-2.5-14.1.el7.x86_64
krb5-devel-1.15.1-34.el7.x86_64
1:openssl-devel-1.0.2k-16.el7.x86_64	# 加密工具包，用于各种加密算法和协议。
e2fsprogs-1.42.9-13.el7.x86_64
xorg-x11-proto-devel-2018.4-1.el7.noarch
libxcb-1.13-1.el7.x86_64
libjpeg-turbo-1.2.90-6.el7.x86_64
libmng-1.0.10-14.el7.x86_64
qt5-qttools-common-5.9.2-1.el7.noarch
2:libogg-1.3.0-7.el7.x86_64
libICE-devel-1.0.9-9.el7.x86_64
libuuid-2.23.2-59.el7.x86_64
libSM-devel-1.2.2-2.el7.x86_64
unixODBC-2.3.1-11.el7.x86_64
postgresql-libs-9.2.24-1.el7_5.x86_64
libblkid-2.23.2-59.el7.x86_64
1:libvorbis-1.3.3-8.el7.1.x86_64
libjpeg-turbo-devel-1.2.90-6.el7.x86_64
2:libpng-devel-1.5.13-7.el7_2.x86_64
freetype-devel-2.8-12.el7.x86_64
libdvdread-5.0.3-3.el7.x86_64
gsm-1.0.13-11.el7.x86_64
orc-0.4.26-1.el7.x86_64
gl-manpages-1.1-7.20130122.el7.noarch
libdrm-devel-2.4.91-3.el7.x86_64
libdvdnav-5.0.3-1.el7.x86_64
libmng-devel-1.0.10-14.el7.x86_64
libmount-2.23.2-59.el7.x86_64
libuuid-devel-2.23.2-59.el7.x86_64
1:libtheora-1.1.1-8.el7.x86_64
flac-libs-1.3.0-5.el7_1.x86_64
libsndfile-1.0.25-10.el7.x86_64
xcb-util-wm-0.4.1-5.el7.x86_64
xcb-util-keysyms-0.4.0-1.el7.x86_64
xcb-util-renderutil-0.3.9-3.el7.x86_64
xcb-util-0.4.0-2.el7.x86_64
xcb-util-image-0.4.0-2.el7.x86_64
libXau-devel-1.0.8-2.1.el7.x86_64
libxcb-devel-1.13-1.el7.x86_64
gstreamer-tools-0.10.36-7.el7.x86_64
gstreamer-0.10.36-7.el7.x86_64
librsvg2-2.40.20-1.el7.x86_64
pcre2-utf16-10.23-2.el7.x86_64
xml-common-0.6.3-39.el7.noarch
iso-codes-3.46-2.el7.noarch
1:libglvnd-opengl-1.0.1-0.8.git5baa1e5.el7.x86_64
bluez-libs-5.44-4.el7_4.x86_64
1:libglvnd-gles-1.0.1-0.8.git5baa1e5.el7.x86_64
expat-devel-2.1.0-10.el7_3.x86_64
fontconfig-devel-2.13.0-4.3.el7.x86_64
libasyncns-0.8-7.el7.x86_64
opus-1.0.2-6.el7.x86_64
soundtouch-1.4.0-9.el7.x86_64
mesa-libGLU-9.0.0-4.el7.x86_64
libvisual-0.4.0-16.el7.x86_64
qt5-rpm-macros-5.9.2-3.el7.noarch
cdparanoia-libs-10.2-17.el7.x86_64
libvpx-1.3.0-5.el7_0.x86_64
libX11-common-1.6.5-2.el7.noarch
libX11-1.6.5-2.el7.x86_64
libX11-devel-1.6.5-2.el7.x86_64
libXext-devel-1.3.3-3.el7.x86_64
libXfixes-devel-5.0.3-1.el7.x86_64
libXrender-devel-0.9.10-1.el7.x86_64
qt3-3.3.8b-51.el7.x86_64
pulseaudio-libs-10.0-5.el7.x86_64
pulseaudio-libs-glib2-10.0-5.el7.x86_64
libXft-devel-2.3.2-2.el7.x86_64
libXcursor-devel-1.1.15-1.el7.x86_64
libXrandr-devel-1.5.1-2.el7.x86_64
libXdamage-devel-1.1.4-4.1.el7.x86_64
libXinerama-devel-1.1.3-2.1.el7.x86_64
libXxf86vm-devel-1.1.4-1.el7.x86_64
libXt-devel-1.1.5-3.el7.x86_64
libXv-1.0.11-1.el7.x86_64
gstreamer-plugins-base-0.10.36-10.el7.x86_64
libXv-devel-1.0.11-1.el7.x86_64
pulseaudio-libs-devel-10.0-5.el7.x86_64
libXi-devel-1.7.9-1.el7.x86_64
glx-utils-8.3.0-10.el7.x86_64
qt5-qtbase-common-5.9.2-3.el7.noarch  # QT肯定是要安装的，wireshark-gnome UI就是QT开发的吧
qt5-qtbase-5.9.2-3.el7.x86_64
qt5-qtbase-gui-5.9.2-3.el7.x86_64
qt5-qttools-libs-designer-5.9.2-1.el7.x86_64
qt5-qttools-libs-designercomponents-5.9.2-1.el7.x86_64
qt5-qttools-libs-help-5.9.2-1.el7.x86_64
qt5-qtserialport-5.9.2-1.el7.x86_64
qt5-qtxmlpatterns-5.9.2-1.el7.x86_64
qt5-qtdeclarative-5.9.2-1.el7.x86_64
1:qt5-qtenginio-1.6.2-2.el7.x86_64
qt5-qtwebchannel-5.9.2-1.el7.x86_64
qt5-qtgraphicaleffects-5.9.2-1.el7.x86_64
qt5-designer-5.9.2-1.el7.x86_64
qt5-qtlocation-5.9.2-1.el7.x86_64
qt5-qtsensors-5.9.2-1.el7.x86_64
qt5-qtconnectivity-5.9.2-1.el7.x86_64
qt5-doctools-5.9.2-1.el7.x86_64
qt5-qtimageformats-5.9.2-1.el7.x86_64
qt5-qt3d-5.9.2-1.el7.x86_64
qt5-qtx11extras-5.9.2-1.el7.x86_64
qt5-linguist-5.9.2-1.el7.x86_64
qt5-qtscript-5.9.2-1.el7.x86_64
qt5-qtsvg-5.9.2-1.el7.x86_64
qt5-qttools-5.9.2-1.el7.x86_64
qt5-qtwebsockets-5.9.2-1.el7.x86_64
qt-settings-19-23.8.el7.noarch
1:qt-4.8.7-2.el7.x86_64
1:qt-x11-4.8.7-2.el7.x86_64
1:libglvnd-core-devel-1.0.1-0.8.git5baa1e5.el7.x86_64
1:libglvnd-devel-1.0.1-0.8.git5baa1e5.el7.x86_64
mesa-libGL-devel-18.0.5-3.el7.x86_64
mesa-libGLU-devel-9.0.0-4.el7.x86_64
mesa-libEGL-devel-18.0.5-3.el7.x86_64
qt5-qtbase-devel-5.9.2-3.el7.x86_64
qt5-qtdeclarative-devel-5.9.2-1.el7.x86_64
libmpcdec-1.2.6-12.el7.x86_64
fftw-libs-double-3.3.3-8.el7.x86_64
libofa-0.9.3-24.el7.x86_64
gstreamer-plugins-bad-free-0.10.23-23.el7.x86_64
qt5-qtmultimedia-5.9.2-1.el7.x86_64
libsmartcols-2.23.2-59.el7.x86_64
util-linux-2.23.2-59.el7.x86_64
qt5-qtmultimedia-devel-5.9.2-1.el7.x86_64
qt5-qtwebchannel-devel-5.9.2-1.el7.x86_64
qt5-qt3d-devel-5.9.2-1.el7.x86_64
qt5-qtlocation-devel-5.9.2-1.el7.x86_64
qt5-qtconnectivity-devel-5.9.2-1.el7.x86_64
qt5-qttools-devel-5.9.2-1.el7.x86_64
qt5-qtscript-devel-5.9.2-1.el7.x86_64
qt5-qtserialport-devel-5.9.2-1.el7.x86_64
qt5-qtsvg-devel-5.9.2-1.el7.x86_64
qt5-qtxmlpatterns-devel-5.9.2-1.el7.x86_64
qt5-qtwebsockets-devel-5.9.2-1.el7.x86_64
1:qt5-qtenginio-devel-1.6.2-2.el7.x86_64
qt5-qtx11extras-devel-5.9.2-1.el7.x86_64
qt5-qtsensors-devel-5.9.2-1.el7.x86_64
qt3-devel-3.3.8b-51.el7.x86_64
1:qt-devel-4.8.7-2.el7.x86_64
1:qt-mysql-4.8.7-2.el7.x86_64
1:qt-odbc-4.8.7-2.el7.x86_64
1:qt-postgresql-4.8.7-2.el7.x86_64
qt5-qtquickcontrols2-5.9.2-1.el7.x86_64
qt5-qtcanvas3d-5.9.2-1.el7.x86_64
qt5-qtquickcontrols-5.9.2-1.el7.x86_64
qt5-qtwayland-5.9.2-1.el7.x86_64
qt5-qtserialbus-5.9.2-1.el7.x86_64
qt5-qtbase-postgresql-5.9.2-3.el7.x86_64
qt5-qtbase-mysql-5.9.2-3.el7.x86_64
qt5-qtbase-odbc-5.9.2-3.el7.x86_64
qt3-ODBC-3.3.8b-51.el7.x86_64
qt3-PostgreSQL-3.3.8b-51.el7.x86_64
qt3-MySQL-3.3.8b-51.el7.x86_64
qt5-qttranslations-5.9.2-1.el7.noarch
qt5-qtdoc-5.9.2-1.el7.noarch
libsigc++20-2.10.0-1.el7.x86_64
glibmm24-2.56.0-1.el7.x86_64
kernel-devel-3.10.0-957.el7.x86_64
mlocate-0.26-8.el7.x86_64
libglade2-2.6.4-11.el7.x86_64
xorg-x11-xbitmaps-1.1.1-6.el7.noarch
libXp-1.0.2-2.1.el7.x86_64
libXxf86misc-1.0.3-7.1.el7.x86_64
xorg-x11-server-utils-7.7-20.el7.x86_64
xorg-x11-xinit-1.3.4-2.el7.x86_64
motif-2.3.4-14.el7_5.x86_64
mesa-libGLw-8.0.0-4.el7.x86_64
wireshark-gnome-1.10.14-16.el7.x86_64
llvm-private-6.0.1-2.el7.x86_64
libXmu-devel-1.1.2-2.el7.x86_64
libXp-devel-1.0.2-2.1.el7.x86_64
motif-devel-2.3.4-14.el7_5.x86_64
mesa-filesystem-18.0.5-3.el7.x86_64
mesa-dri-drivers-18.0.5-3.el7.x86_64
mesa-libGLw-devel-8.0.0-4.el7.x86_64
mesa-libxatracker-18.0.5-3.el7.x86_64
mesa-libGLES-18.0.5-3.el7.x86_64
mesa-private-llvm-3.9.1-3.el7.x86_64
elfutils-libelf-0.172-2.el7.x86_64
rpm-4.11.3-35.el7.x86_64
rpm-libs-4.11.3-35.el7.x86_64
rpm-build-libs-4.11.3-35.el7.x86_64 #如果想把wireshark编译后为rpm安装包，也需要安装
elfutils-libs-0.172-2.el7.x86_64
elfutils-0.172-2.el7.x86_64
dwz-0.11-3.el7.x86_64
perl-Git-1.8.3.1-19.el7.noarch
patch-2.7.1-10.el7_5.x86_64
gdb-7.6.1-114.el7.x86_64
perl-Thread-Queue-3.02-2.el7.noarch
perl-srpm-macros-1-8.el7.noarch
redhat-rpm-config-9.1.0-87.el7.noarch
bzip2-1.0.6-13.el7.x86_64
rpm-build-4.11.3-35.el7.x86_64
rpm-python-4.11.3-35.el7.x86_64
bison-3.0.4-2.el7.x86_64
flex-2.5.37-6.el7.x86_64
byacc-1.9.20130304-3.el7.x86_64
nss-pem-1.0.3-5.el7.x86_64
libcurl-7.29.0-51.el7.x86_64
curl-7.29.0-51.el7.x86_64 
libcurl-devel-7.29.0-51.el7.x86_64
libicu-50.1.2-17.el7.x86_64
harfbuzz-icu-1.7.5-2.el7.x86_64
libicu-devel-50.1.2-17.el7.x86_64
wayland-devel-1.15.0-1.el7.x86_64
gdk-pixbuf2-devel-2.36.12-3.el7.x86_64
libXcomposite-devel-0.4.4-4.1.el7.x86_64
pixman-devel-0.34.0-1.el7.x86_64
cairo-devel-1.15.12-3.el7.x86_64
cairo-gobject-devel-1.15.12-3.el7.x86_64
wayland-protocols-devel-1.14-1.el7.noarch
fribidi-devel-1.0.2-1.el7.x86_64
libxkbcommon-devel-0.7.1-1.el7.x86_64
atk-devel-2.28.1-1.el7.x86_64
1:dbus-devel-1.10.24-12.el7.x86_64
at-spi2-core-devel-2.28.0-1.el7.x86_64
at-spi2-atk-devel-2.26.2-1.el7.x86_64
graphite2-devel-1.3.10-1.el7_3.x86_64
harfbuzz-devel-1.7.5-2.el7.x86_64
pango-devel-1.42.4-1.el7.x86_64
libepoxy-devel-1.5.2-1.el7.x86_64
gtk3-devel-3.22.30-3.el7.x86_64
4:perl-libs-5.16.3-293.el7.x86_64
4:perl-5.16.3-293.el7.x86_64 #需要用到perl语言，比如python3
>

## Python3安装

下载后直接编译安装

```shell
tar -xvf /home/Private/Python-3.7.4.tgz -C /usr/local/src/
cd /usr/local/src/Python-3.7.4
./configure --enable-optimizations && make -j24 && make install
```

剩下的就交给时间吧，这个过程差不多需要10分钟，可以去放水了😂

等待安装完成如下：

[![Python3 Install](https://i.loli.net/2019/08/14/wL2crmtlyvsXYDS.png)](https://i.loli.net/2019/08/14/wL2crmtlyvsXYDS.png)

## Cmake安装

C编译器安装，推荐下载`cmake-3.15.2-Linux-x86_64.sh`, 可以直接安装，方便快捷

执行`./cmake-3.15.2-Linux-x86_64.sh`即可,默认安装路径是当前路径，支持的参数有：

[![cmake_install](https://i.loli.net/2019/08/14/xkTbuBCJLFl8ZIp.png)](https://i.loli.net/2019/08/14/xkTbuBCJLFl8ZIp.png)

还需要添加cmake到环境中。

`export PATH=/usr/local/src/cmake-3.15.2-Linux-x86_64/bin:${PATH}`

## libpcap安装

标准的三步走策略，减压->编译->安装，搞定！

```shell
tar -xvf tar -xvf /home/Private/Compressed/libpcap-libpcap-1.9.0.tar.gz -C ./
cd libpcap-libpcap-1.9.0
./configure && make && make install
```

安装完成

[![wireshark_cap](https://i.loli.net/2019/08/14/nWiGwjxbhevlUc7.png)](https://i.loli.net/2019/08/14/nWiGwjxbhevlUc7.png)

## Wireshark安装

最新版由于不想v1.x那样三步走了，在github上下载的需要用cmake编译，所以前面比较费劲。如果前面的步骤都做完了，那么剩下的就很简单了。三步走。。

```shell
tar -xvf wireshark-3.1.0.tar.xz -C /usr/local/src
cd /usr/local/src
cmake ./
make && make install
```

等待安装完成，无任何报错。

[![wireshark](https://i.loli.net/2019/08/14/a1Pqe5Nx94W8KQL.png)](https://i.loli.net/2019/08/14/a1Pqe5Nx94W8KQL.png)

然后试着执行`wireshark`，如果顺利打开图形界面，操作都正常，那么恭喜！
可以通过执行`wireshark -v`来确认版本。

# 总结

这里没有讲解wireshark的用法，只能说很强大！由于之前用系统自带v1.10.14的版本，遇到了问题，所以考虑更新到最新版本，没想到遇到了很多坑，遂记录下来。

之前遇到一些坑，如下

1. 提示Cmake版本过低

[![cmake版本过低](https://i.loli.net/2019/08/14/YzoVh2sbDjBQOHi.png)](https://i.loli.net/2019/08/14/YzoVh2sbDjBQOHi.png)

2. 缺少glib2

[![缺少glib2](https://i.loli.net/2019/08/14/JLnyE1CbviD7kZI.png)](https://i.loli.net/2019/08/14/JLnyE1CbviD7kZI.png)

3. 缺少gcrypt.

[![缺少gcrypt](https://i.loli.net/2019/08/14/jgFzx9a67LlMWIh.png)](https://i.loli.net/2019/08/14/jgFzx9a67LlMWIh.png)

4. 需要Python3, 系统自带的Python2肯定不满足

[![缺少Python3](https://i.loli.net/2019/08/14/h2w3lGEPpO4dmvf.png)](https://i.loli.net/2019/08/14/h2w3lGEPpO4dmvf.png)


**推荐关注博主公众号，获取最新的文章😀**

[![服务器测试与运维](https://i.loli.net/2019/08/01/5d42e3a801fb564745.jpg)](https://i.loli.net/2019/08/01/5d42e3a801fb564745.jpg)


> 📌转载请注明来源，版权归作者**[@hualong1009](https://hualong1009.github.io)**所有, 谢谢
> 
























