Date: 2022-06-24

# High Availbility, Virtual IPs, and Wireguard

## Overview

I recently took the time to sit-down and reconfigure Wireguard on OPNSense. Having just redone my entire IP Addressing scheme to accomodate a highly-available setup, I just blew away everything I had previously setup for Wireguard, and started fresh.

**Just recording references for now. More to come!**

## TL;DR

In a highly available OPNsense configuration, you can use your Virtual IPs with WireGuard, as long as you don't need individual static IPs on the network interface(s) the VIP(s) are on. (e.g. in my setup, I'll never need to hit the WAN interfaces on my firewalls individually, so I can use the WAN VIP for external Wireguard peers)

## References

### Articles and Blogs

* [WireGuard Road Warrior Setup](https://docs.opnsense.org/manual/how-tos/wireguard-client.html)
* [ArchWiki - WireGuard](https://wiki.archlinux.org/title/WireGuard)
* [WIREGUARD ENABLE DEBUG LOGGING TO FIX NETWORK ISSUES](https://www.the-digital-life.com/wireguard-enable-logging/)
* [How to configure WireGuard VPN client with NetworkManager GUI](https://www.xmodulo.com/wireguard-vpn-network-manager-gui.html)
* [FOUR WAYS TO VIEW WIREGUARD LOGS](https://www.procustodibus.com/blog/2021/03/wireguard-logs)
* [Wireguard site-to-site setup only works on default WAN IP not VIP](https://forum.opnsense.org/index.php?topic=21445.0)
* [WireShark - WireGuard](https://wiki.wireshark.org/WireGuard)
* [WireGuard - Official Site](https://www.wireguard.com/)
* [TCPDump: Capture and Record Specific Protocols / Port](https://www.cyberciti.biz/faq/tcpdump-capture-record-protocols-port/)
* [Simple and Secure VPN in FreeBSD – Introducing WireGuard](https://forums.freebsd.org/threads/simple-and-secure-vpn-in-freebsd-introducing-wireguard.78628/)
* [Wireguard Sending Handshake initiation with No Connection](https://www.reddit.com/r/WireGuard/comments/qgjw1f/wireguard_sending_handshake_initiation_with_no/)
* [Wireguard Unable to Complete Handshake on Android only 4G network](https://serverfault.com/questions/1058060/wireguard-unable-to-complete-handshake-on-android-only-4g-network)
* [Wireguard server thinks client is at wrong endpoint](https://stackoverflow.com/questions/69792700/wireguard-server-thinks-client-is-at-wrong-endpoint)
* [Wireguard not completing handshake](https://www.ringingliberty.com/2020/10/26/wireguard-not-completing-handshake/)
* [error: handshake did not complete after 5 seconds](https://github.com/angristan/wireguard-install/issues/39)
* [Wireguard server didnot send a handshake](https://stackoverflow.com/questions/69493519/wireguard-server-didnot-send-a-handshake)
* [Wireguard client not receiving](https://forum.opnsense.org/index.php?topic=19678.0)
* [Wireguard VPN does not receive data from server](https://stackoverflow.com/questions/56534747/wireguard-vpn-does-not-receive-data-from-server)
* [WireGuard VPN Troubleshooting](https://www.pixelstech.net/article/1639494752-WireGuard-VPN-Troubleshooting)
* [Comcast Xfi potentially blocking port forwarding on 51820](https://www.reddit.com/r/WireGuard/comments/fsolnu/comcast_xfi_potentially_blocking_port_forwarding/)
* [How to install and use WireGuard on iOS](https://vladtalks.tech/vpn/setup-wireguard-on-ios)
* [Wireguard handshake](https://forum.opnsense.org/index.php?topic=26086.0)
* [No handshakes, no data transfer](https://www.reddit.com/r/WireGuard/comments/bbh80f/no_handshakes_no_data_transfer/)
* [FreeBSD IPFW rules for WireGuard](https://sirtoffski.github.io/docs/freebsd-ipfw/)
* [Can't get Wireguard running on Freebsd to ping](https://serverfault.com/questions/1035735/cant-get-wireguard-running-on-freebsd-to-ping)
* [Wireguard suddenly refuses to handshake](https://forum.netgate.com/topic/167956/wireguard-suddenly-refuses-to-handshake)
* [Howto: WireGuard on OpenBSD](https://ianix.com/wireguard/openbsd-howto.html)
* [WireGuard dabblings](https://try.popho.be/wg.html)
* [Wireguard and freebsd](https://www.reddit.com/r/WireGuard/comments/jfvpuu/wireguard_and_freebsd/)
* [BUILDING AN OPENBSD WIREGUARD SERVER](https://www.lonecpluspluscoder.com/2019/12/27/building-an-openbsd-wireguard-server/)
* [WireGuard on FreeBSD Quick Look: Testing VPN in Jail Network](https://genneko.github.io/playing-with-bsd/networking/freebsd-wireguard-quicklook/)
* [Simple and Secure VPN in FreeBSD – Introducing WireGuard](https://klarasystems.com/articles/simple-and-secure-vpn-in-freebsd/)
* [The Unofficial Non-Definitive WireGuard Troubleshooting Page](https://www.rpiathome.com/2020/05/13/the-unofficial-non-definitive-wireguard-troubleshooting-page/)
* [Wireguard handshakes but no ping through](https://bbs.archlinux.org/viewtopic.php?id=248407)
* [How can I debug what could be wrong on a Wireguard connection?](https://askubuntu.com/questions/1310187/how-can-i-debug-what-could-be-wrong-on-a-wireguard-connection)
* [How to debug wireguard?](https://www.reddit.com/r/WireGuard/comments/kl8il1/how_to_debug_wireguard/)
* [Ensure all traffic is routed through tunnel](https://www.reddit.com/r/WireGuard/comments/loye8j/ensure_all_traffic_is_routed_through_tunnel/)
* [Logging unauthorised attempts](https://www.reddit.com/r/WireGuard/comments/vfbkxa/logging_unauthorised_attempts/)
* [Connected successfully, but no traffic is routed](https://www.reddit.com/r/WireGuard/comments/dqp79a/connected_successfully_but_no_traffic_is_routed/)
* [Wireguard VPN not passing traffic](https://www.reddit.com/r/opnsense/comments/qctebr/wireguard_vpn_not_passing_traffic/)
* [Wireguard not routing traffic from client to other servers on the network](https://unix.stackexchange.com/questions/530790/wireguard-not-routing-traffic-from-client-to-other-servers-on-the-network)
* [Wireguard does not seem to be passing traffic through, assigning strange IP](https://stackoverflow.com/questions/64970922/wireguard-does-not-seem-to-be-passing-traffic-through-assigning-strange-ip)
* [WireGuard traffic not making it through tunnel](https://serverfault.com/questions/1095254/wireguard-traffic-not-making-it-through-tunnel)
* [Working VPN-gateway configs for WireGuard](https://jrs-s.net/2018/08/05/working-vpn-gateway-configs-for-wireguard/)
* [WireGuard+OPNsense works great, but WireGuard+OPNsense that is OpenVPN fails](https://www.reddit.com/r/OPNsenseFirewall/comments/gkgtmj/wireguardopnsense_works_great_but/)
* [OPNsense WireGuard - issues with connectivity](https://www.reddit.com/r/OPNsenseFirewall/comments/pplrkl/opnsense_wireguard_issues_with_connectivity/)
* [Wireguard Issues](https://forum.opnsense.org/index.php?topic=14403.0)
* [Wireguard road warrior (followed default guide) - all work but cant ping clients](https://forum.opnsense.org/index.php?topic=25784.0)
* [WireGuard Issue's while setting up](https://forum.opnsense.org/index.php?topic=24048.0)
* [Wireguard is connected, but not traffic](https://forum.opnsense.org/index.php?topic=20614.0)
* [How Do I Set Up WireGuard for OPNsense?](https://www.sunnyvalley.io/docs/network-security-tutorials/how-to-setup-wireguard-on-opnsense)
* [Make local resources available when connected to Wireguard VPN](https://unix.stackexchange.com/questions/638889/make-local-resources-available-when-connected-to-wireguard-vpn)
* [Talk Tech to Me: Configuring WireGuard: The Hottest New VPN Technology](https://www.comptia.org/blog/configuring-wireguard-vpn)
* [Allow local network access even when wireguard is up](https://www.reddit.com/r/WireGuard/comments/n1bqf8/allow_local_network_access_even_when_wireguard_is)
* [WireGuard, no internet](https://www.reddit.com/r/PFSENSE/comments/lzqsds/wireguard_no_internet/)
* [How much memory does wireguard server consume on a ubuntu 19 server?](https://www.reddit.com/r/WireGuard/comments/dcuvph/how_much_memory_does_wireguard_server_consume_on/)
* [Viewing WireGuard Traffic with Tcpdump](https://nickb.dev/blog/viewing-wireguard-traffic-with-tcpdump/)
* [Wireguard Remote Access configuration. No access to Internet](https://forum.netgate.com/topic/161199/wireguard-remote-access-configuration-no-access-to-internet/6)
* [Route all traffic through Wireguard peer](https://serverfault.com/questions/1074729/route-all-traffic-through-wireguard-peer)
* [Routing traffic through Wireguard peers](https://www.janhouse.lv/blog/linux/routing-traffic-through-wireguard-peers/)
* [How to Set Up WireGuard VPN on Ubuntu 20.04](https://linuxize.com/post/how-to-set-up-wireguard-vpn-on-ubuntu-20-04/)
* [Use wg-quick to make your Wireguard tunnels easy and persistent on Linux](https://www.adamintech.com/use-wg-quick-to-make-your-wireguard-tunnels-easy-and-persistent-on-linux/)
* [wg-quick manpage](https://manpages.debian.org/unstable/wireguard-tools/wg-quick.8.en.html)
* [simple WireGuard setup - it's just not working](https://forum.opnsense.org/index.php?topic=23517.0)
* [Unbound does not allow access WireGuard interface despite told to](https://github.com/opnsense/core/issues/4142)
* [Firewall Rules - Wireguard Interface missing](https://forum.opnsense.org/index.php?topic=18267.0)
* [Setting up WireGuard on opnsense != fun](https://www.reddit.com/r/OPNsenseFirewall/comments/pxgvyd/setting_up_wireguard_on_opnsense_fun/)
* [How to Configure the WireGuard VPN Server in OPNsense](https://homenetworkguy.com/how-to/configure-wireguard-opnsense)
* [WireGuard Road Warrior Setup: How to access VLAN?](https://forum.opnsense.org/index.php?topic=24571.0)

### Videos

* [WireGuard: Next Generation Secure Network Tunnel](https://www.youtube.com/watch?v=88GyLoZbDNw)
* [How To Build Your Own Wireguard VPN Server in The Cloud](https://www.youtube.com/watch?v=7yC-gJtl9mQ)
* [WireGuard VPN with TLS Tunneling](https://www.youtube.com/watch?v=HwYIrpOr1j0)
* [What is Wireguard? A "New" VPN Protocol + How it Compares to OpenVPN](https://www.youtube.com/watch?v=mxpHRdO4rDU)
* [WireGuard explained + BEST VPNs that support it | WireGuard VPN](https://www.youtube.com/watch?v=KzcCrW0J61A)

### Source Code

* [WireGuard/wireguard-android](https://github.com/WireGuard/wireguard-android)
* [WireGuard/wireguard-windows](https://github.com/WireGuard/wireguard-windows)
* [WireGuard/wireguard-apple](https://github.com/WireGuard/wireguard-apple)
* [WireGuard/wireguard-freebsd](https://github.com/WireGuard/wireguard-freebsd)
