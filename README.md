# WRITE-UP: IP Puller via STUN - Wireshark Edition

##  Overview
This tool extracts peer public IPs from STUN traffic, commonly exposed by P2P games and voice chats.

It uses Wireshark's tshark to sniff live UDP traffic.

##  How It Works

1. Get Own Public IP
   Fetch public IP via api.ipify.org to filter out our own address from results.
   Without this, you will see your own IP looping.

2. Locate Tshark
   Auto-detects tshark.exe from:
   C:\Program Files\Wireshark\tshark.exe
   C:\Program Files (x86)\Wireshark\tshark.exe

3. List Interfaces
   tshark -D lists all capture interfaces.
   User selects which one to use.

4. Capture & Filter
   tshark -i %interface% -f udp -Y "stun.type == 0x0101 && stun.att.type == 0x0020 && stun.att.ipv4 != %publicip%" -T fields -e stun.att.ipv4

   Filter logic:
   udp : only UDP for performance
   stun.type == 0x0101 : Binding Success Response
   stun.att.type == 0x0020 : XOR-MAPPED-ADDRESS attribute
   stun.att.ipv4 != publicip : exclude self

##  Why STUN Leaks IP
STUN is used for NAT traversal. When two peers want to connect directly, they exchange mapped addresses. In many games, this packet is not relayed or encrypted, so the real public IP is visible.

##  Limitations
- Will not get real IP if target uses TURN-only relay
- May get relay server IP instead of peer IP
- Requires admin privilege to capture

##  Defense
- Enforce TURN relay
- Use VPN
- Encrypt STUN or use custom obfuscation

##  Legal
For educational and lab use only. Only test on networks and sessions you own.

Author: urfemthings 

just some good stuff ;) ;3
