

## これは何？
物理インターフェイスを仮想で複数に分ける技術

## なぜ使う？
１つのポートで複数のネットワークを制作できるのでコスト削減、通信分離が簡単になる

## 基本設定
[Switch1] --- [Switch2]
(VLAN10)    (VLAN20)

## Switch1
enter candidate
set / interface ethernet-1/1 admin-state enable
set / interface ethernet-1/1 vlan-tagging true
set / interface ethernet-1/1 subinterface 10 type bridged
set / interface ethernet-1/1 subinterface 10 vlan encap single-tagged vlan-id 10
set / interface ethernet-1/1 subinterface 20 type bridged
set / interface ethernet-1/1 subinterface 20 vlan encap single-tagged vlan-id 20
set / network-instance vlan10 type mac-vrf
set / network-instance vlan10 interface ethernet-1/1.10
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
set / network-instance vlan10 type mac-vrf
set / network-instance vlan10 interface ethernet-1/1.10
set / network-instance vlan20 type mac-vrf
set / network-instance vlan20 interface ethernet-1/1.20
commit now
## 確認コマンド
```コマンド```
<font color="#c00000">A:admin@switch1# show network-instance interfaces</font>
===============================================================================================================================================================
Net instance    : mgmt
Interface       : mgmt0.0
Oper state      : up
Ip mtu          : 1500
  Prefix                                    Origin       Status
  ===============================================================================================
  172.20.20.2/24                            dhcp         preferred
  3fff:172:20:20::2/64                      dhcp         preferred
  fe80::42:acff:fe14:1402/64                link-layer   preferred
===============================================================================================================================================================
<font color="#c00000">Net instance    : vlan10</font>
<font color="#c00000">Interface       : ethernet-1/1.10</font>
<font color="#c00000">Type            : bridged</font>

===============================================================================================================================================================
<font color="#c00000">Net instance    : vlan20</font>
<font color="#c00000">Interface       : ethernet-1/1.20</font>
<font color="#c00000">Type            : bridged</font>
## → vlan10, vlan20が表示されればOK
## 注意点
- vlan-tagging true を忘れるとエラーになる
- subinterfaceとVLAN IDは一致させる必要はない（.10がVLAN20でもOK）
- trunk = 複数VLAN、access = 1つのVLAN（untagged）