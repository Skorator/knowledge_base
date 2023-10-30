

1. Remove classic Networking and switch to `systemd-networkd`
### General Setup
- Install systemd-networkd (remove dnsmasq)

```
# uninstall classic networking
pi@raspberrypi:~ $ sudo -Es   # if not already done
root@raspberrypi:~ # systemctl daemon-reload
root@raspberrypi:~ # systemctl disable --now ifupdown dhcpcd dhcpcd5 isc-dhcp-client isc-dhcp-common rsyslog
root@raspberrypi:~ # apt --autoremove purge ifupdown dhcpcd dhcpcd5 isc-dhcp-client isc-dhcp-common rsyslog
root@raspberrypi:~ # rm -r /etc/network /etc/dhcp

# setup/enable systemd-resolved and systemd-networkd
root@raspberrypi:~ # systemctl disable --now avahi-daemon libnss-mdns
root@raspberrypi:~ # apt --autoremove purge avahi-daemon
root@raspberrypi:~ # apt install libnss-resolve
root@raspberrypi:~ # ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
root@raspberrypi:~ # apt-mark hold avahi-daemon dhcpcd dhcpcd5 ifupdown isc-dhcp-client isc-dhcp-common libnss-mdns openresolv raspberrypi-net-mods rsyslog
root@raspberrypi:~ # systemctl enable systemd-networkd.service systemd-resolved.service
root@raspberrypi:~ # exit
pi@raspberrypi:~ $
```

### Configure `wpa_supplicant` access point

```
rpi ~$ sudo -Es   # if not already done
rpi ~# cat > /etc/wpa_supplicant/wpa_supplicant-wlan0.conf <<EOF
country=DE
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="Tapir"
    mode=2
    frequency=2437
    # key_mgmt=NONE   # uncomment this for an open hotspot
    # delete next 3 lines if key_mgmt=NONE
    key_mgmt=WPA-PSK
    proto=RSN WPA
    psk="12345678"
}
EOF

rpi ~# chmod 600 /etc/wpa_supplicant/wpa_supplicant-wlan0.conf
rpi ~# systemctl disable wpa_supplicant.service
rpi ~# systemctl enable wpa_supplicant@wlan0.service
rpi ~# rfkill unblock wlan
```

### Tell `wpa_supplicant` to use the bridge
1. Define interfaces and networks for the manager

```
rpi ~# cat > /etc/systemd/network/02-br0.netdev <<EOF
[NetDev]
Name=br0
Kind=bridge
EOF

rpi ~# cat > /etc/systemd/network/08-wlan0.network <<EOF
[Match]
Name=wlan0
EOF

rpi ~# cat > /etc/systemd/network/12-br0_add-enxa0cec88b334c.network <<EOF
[Match]
Name=enxa0cec88b334c
[Network]
Bridge=br0
EOF

rpi ~# cat > /etc/systemd/network/16-br0_up.network <<EOF
[Match]
Name=br0
[Network]
Address=192.168.4.1/24
MulticastDNS=yes
IPMasquerade=yes
DHCPServer=yes
[DHCPServer]
DNS=84.200.69.80 1.1.1.1
EOF
```

2. Open editor
```
rpi ~# systemctl edit wpa_supplicant@wlan0.service
```

3. Enter and close editor after

```
[Service]
ExecStartPre=/sbin/iw dev %i set type __ap
ExecStartPre=/bin/ip link set %i master br0

ExecStart=
ExecStart=/sbin/wpa_supplicant -c/etc/wpa_supplicant/wpa_supplicant-%I.conf -Dnl80211,wext -i%I -bbr0

ExecStopPost=-/bin/ip link set %i nomaster
ExecStopPost=-/sbin/iw dev %i set type managed
```

Reboot and check if the bridge works. The mobile phone and the laptop should have an ip address from subnet 192.168.4.0/24 and you should be able to ping the mobile phone from the laptop and vice versa.


### Configure the up-link eth1 and bridge to eth0

```
rpi ~$ sudo -Es
rpi ~# cat > /etc/systemd/network/04-eth1.network <<EOF
[Match]
Name=enxa0cec88b334c
[Network]
DHCP=yes
MulticastDNS=yes
EOF
```

```
rpi ~# systemctl daemon-reload
rpi ~# systemctl restart systemd-networkd.service
rpi ~# exit
rpi ~$
```
