## これは何？
Trunk =複数のVLANを通す役割
Access=単一のVLANだけ（PCなどのデバイスに使用)

## なぜ使う？
スイッチやルーターに対して複数のVLANを通す必要がある際に使用する
簡潔に利点を出すとAccessはセキュリティ、Trunkは複数 VLANを 1 本で通せるようになる
## 基本設定
[Switch1] ========= [Switch2]
   |                  Trunk               |
  PC1                                   PC2
(Access)                            (Access) 
(VLAN10)                         (VLAN10)

## Switch1
enter candidate
set / interface ethernet-1/1 admin-state enable
set / interface ethernet-1/1 vlan-tagging true
set / interface ethernet-1/1 subinterface 10 type bridged
set / interface ethernet-1/1 subinterface 10 vlan encap single-tagged vlan-id 10
set / interface ethernet-1/1 subinterface 20 type bridged
set / interface ethernet-1/1 subinterface 20 vlan encap single-tagged vlan-id 20
set / interface ethernet-1/2 admin-state enable
set / interface ethernet-1/2 vlan-tagging true
set / interface ethernet-1/2 subinterface 1 type bridged
set / interface ethernet-1/2 subinterface 1 vlan encap untagged
set / network-instance vlan10 type mac-vrf
set / network-instance vlan10 interface ethernet-1/1.10
set / network-instance vlan10 interface ethernet-1/2.1
set / network-instance vlan20 type mac-vrf
set / network-instance vlan20 interface ethernet-1/1.20
commit now

## Switch2
enter candidate
set / interface ethernet-1/1 admin-state enable
set / interface ethernet-1/1 vlan-tagging true
set / interface ethernet-1/1 subinterface 10 type bridged
set / interface ethernet-1/1 subinterface 10 vlan encap single-tagged vlan-id 10
set / interface ethernet-1/1 subinterface 20 type bridged
set / interface ethernet-1/1 subinterface 20 vlan encap single-tagged vlan-id 20
set / interface ethernet-1/2 admin-state enable
set / interface ethernet-1/2 vlan-tagging true
set / interface ethernet-1/2 subinterface 1 type bridged
set / interface ethernet-1/2 subinterface 1 vlan encap untagged
set / network-instance vlan10 type mac-vrf
set / network-instance vlan10 interface ethernet-1/1.10
set / network-instance vlan10 interface ethernet-1/2.1
set / network-instance vlan20 type mac-vrf
set / network-instance vlan20 interface ethernet-1/1.20
commit now

## PC1＆2
ip addr add 192.168.10.10/24 dev eth1 
ip link set eth1 up

ip addr add 192.168.10.20/24 dev eth1 
ip link set eth1 up

## 確認コマンド
<font color="#c00000">A:admin@switch1# show interface ethernet-1/1</font>
ethernet-1/1 is up, speed 25G, type None
  <font color="#c00000">ethernet-1/1.10 is up</font>
    Network-instances:
      * Name: vlan10 (mac-vrf)
    Encapsulation   : <font color="#c00000">vlan-id 10</font>
    Type            : bridged
  <font color="#c00000">ethernet-1/1.20 is up</font>
    Network-instances:
      * Name: vlan20 (mac-vrf)
    Encapsulation   : <font color="#c00000">vlan-id 20</font>
    Type            : bridged
## → 1/1のサブインターフェイスにそれぞれvlan10, vlan20が表示されればOK

<font color="#c00000">A:admin@switch1# show interface ethernet-1/2</font>
ethernet-1/2 is up, speed 25G, type None
  ethernet-1/2.1 is up
    Network-instances:
      * Name: <font color="#c00000">vlan10</font> (mac-vrf)
    Encapsulation   : null
    Type            : bridged
## → 単一のvlan10が表示されればOK

## PC1
<font color="#c00000">/ # ping 192.168.10.20</font>
PING 192.168.10.20 (192.168.10.20): 56 data bytes
64 bytes from 192.168.10.20: seq=0 ttl=64 time=2.400 ms
64 bytes from 192.168.10.20: seq=1 ttl=64 time=0.718 ms
64 bytes from 192.168.10.20: seq=2 ttl=64 time=0.435 ms
^C
--- 192.168.10.20 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.435/1.184/2.400 ms
## → pingが通ればOK
## 注意点
- Access = untagged必須
