---
title: Edgerouter X and Wireguard
categories: [edgerouter,wireguard]
tags: [router,edgerouter,wireguard,vpn,ovpn]
---

# Edgerouter X and Wireguard
This will get Wireguard installed and configured on a Edgerouter or EdgeMAX device.

I started by following [this guide](https://mroach.com/2018/03/policy-based-routing-over-vpn-with-ubiquiti-edgerouter/) for setting up OpenVPN but wanted to change to Wireguard for a faster connection.

I made some alterations from that guide for it to work with Wireguard.

SSH into your router and download the latest version of [wireguard-vyatta-ubnt](https://github.com/WireGuard/wireguard-vyatta-ubnt), there is also a version 1 if you have old firmware on your router, but you should probably upgrade in that case.

```bash
curl -OL https://github.com/WireGuard/wireguard-vyatta-ubnt/releases/download/1.0.20211208-1/e50-v2-v1.0.20211208-v1.0.20210914.deb
sudo dpkg -i e50-v2-v1.0.20211208-v1.0.20210914.deb
rm e50-v2-v1.0.20211208-v1.0.20210914.deb
```

You will need a VPN server to connect to, I used [OVPN](https://www.ovpn.com/). You might get a config file looking like this:

```bash
[Interface]
PrivateKey = thisISyourPRIVATEkey=
Address = 181.91.230.82/32, d332:a4d8:ba9d:5df0:bf6a:31a2:29e6:ebce/128
DNS = 48.67.108.253, 48.67.108.254

[Peer]
PublicKey = thisISyourPUBLICkey=
AllowedIPs = 0.0.0.0/0, ::/0
Endpoint = example.com:1234
```

For all of the following code blocks choose rule numbers and names that you know are not used, otherwise you might overwrite something.

You can use `show` to check if there is anything there, like `show service nat rule 5000`.

```bash
# Copy the PrivateKey exaclty as is into /config/user-data/wg.key
echo "thisISyourPRIVATEkey=" >> /config/user-data/wg.key

configure
# Let's create the Wireguard interface using data from the config file
set interfaces wireguard wg0 description "OVPN Wireguard"
set interfaces wireguard wg0 address 181.91.230.82/32
set interfaces wireguard wg0 listen-port 51820
set interfaces wireguard wg0 route-allowed-ips false
set interfaces wireguard wg0 peer thisISyourPUBLICkey= endpoint example.com:1234
set interfaces wireguard wg0 peer thisISyourPUBLICkey= allowed-ips 0.0.0.0/0
set interfaces wireguard wg0 private-key /config/user-data/wg.key
commit
```

This will write the changes to the router but they will not be persistent,
so if you mess up, just reboot the router. This is good practice, make a commit every few steps and `save` when you are sure everything is good.

Make sure everything is working still, `wg0` should be shown as connected in the web interface of the router.

I don’t want to use the VPN for everything on my network, only select clients. So let’s make a group that I then can add or remove clients from.

```bash
set firewall group address-group vpn_group_wireguard
set firewall group address-group vpn_group_wireguard address 192.168.12.101
set firewall group address-group vpn_group_wireguard address 192.168.12.102
# Only 192.168.12.101 and 192.168.12.102 will use Wireguard in this case

set protocols static table 2 interface-route 0.0.0.0/0 next-hop-interface wg0

set firewall modify ovpn-wireguard rule 12 description "OVPN Stockholm Wireguard"
set firewall modify ovpn-wireguard rule 12 source group address-group vpn_group_wireguard
set firewall modify ovpn-wireguard rule 12 modify table 2

# I use vlans and I want only vlan 12 to use the VPN
set interfaces switch switch0 vif 12 firewall in modify ovpn-wireguard

# To use the whole switch you would instead do:
# set interfaces switch switch0 firewall in modify ovpn-wireguard

# Nat rules
set service nat rule 5000 description "masquerade for wg0"
set service nat rule 5000 outbound-interface wg0
set service nat rule 5000 type masquerade

set firewall name WAN_LOCAL rule 15 action accept
set firewall name WAN_LOCAL rule 15 protocol udp
set firewall name WAN_LOCAL rule 15 description 'WireGuard'
set firewall name WAN_LOCAL rule 15 destination port 51820

commit
# Check that all is good then save
save
```

I had some problems getting the traffic to get through the tunnel, but after checking that everything was setup right I saved and rebooted the router. That did it.

The way I checked that it was working was I added my phone to the `vpn_group_wireguard` and started a music stream, that way I could see which interface was having traffic.

It works well now, adding devices to the wireguard group makes them use the tunnel and and removing them makes them go open.