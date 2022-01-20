# OpenVPN development feed for OpenWrt

## Description

In this feed you find the following two packages:
* kmod-ovpn-dco-dev: ovpn-dco kernel module built out of latest master branch
* openvpn-dco-dev: openvpn software built out of the latest dco branch

These two packages are helpful to test OpenVPN with DCO (Data Channel Offload)
support before it gets release.

## Usage

You must have an OpenWrt buildroot already setup.
To enable this feed add the following to your feeds.conf:
```
src-git openvpndev https://github.com/OpenVPN/openvpn-dev-openwrt.git
```
and then run
```
./scripts/feeds update openvpndev
./scripts/feeds install -p openvpndev kmod-ovpn-dco-dev openvpn-dco-dev
```

You can now select the new packages from the menuconfig interface.
