## これは何？
ループを防ぐプロトコル

## なぜ使う？
冗長構成でブロードキャストストーム防止 
1つのポートをブロック

## 基本設定
　　[SW1]
　　／   ＼
   ／            ＼
[SW2]----[SW3]
## 確認コマンド
## Switch1
enter candidate
set / interface ethernet-1/1 admin-state enable
set / interface ethernet-1/1 vlan-tagging true
set / interface ethernet-1/1 subinterface 1 type bridged
set / interface ethernet-1/1 subinterface 1 vlan encap untagged
set / interface ethernet-1/2 admin-state enable
set / interface ethernet-1/2 vlan-tagging true
set / interface ethernet-1/2 subinterface 1 type bridged
set / interface ethernet-1/2 subinterface 1 vlan encap untagged
set / network-instance default type mac-vrf
set / network-instance default interface ethernet-1/1.1
set / network-instance default interface ethernet-1/2.1
set / network-instance default protocols spanning-tree admin-state enable
set / network-instance default protocols spanning-tree priority 4096
commit now
## Switch2
enter candidate
set / interface ethernet-1/1 admin-state enable
set / interface ethernet-1/1 vlan-tagging true
set / interface ethernet-1/1 subinterface 1 type bridged
set / interface ethernet-1/1 subinterface 1 vlan encap untagged
set / interface ethernet-1/2 admin-state enable
set / interface ethernet-1/2 vlan-tagging true
set / interface ethernet-1/2 subinterface 1 type bridged
set / interface ethernet-1/2 subinterface 1 vlan encap untagged
set / network-instance default type mac-vrf
set / network-instance default interface ethernet-1/1.1
set / network-instance default interface ethernet-1/2.1
set / network-instance default protocols spanning-tree admin-state enable
commit now
## Switch3
enter candidate
set / interface ethernet-1/1 admin-state enable
set / interface ethernet-1/1 vlan-tagging true
set / interface ethernet-1/1 subinterface 1 type bridged
set / interface ethernet-1/1 subinterface 1 vlan encap untagged
set / interface ethernet-1/2 admin-state enable
set / interface ethernet-1/2 vlan-tagging true
set / interface ethernet-1/2 subinterface 1 type bridged
set / interface ethernet-1/2 subinterface 1 vlan encap untagged
set / network-instance default type mac-vrf
set / network-instance default interface ethernet-1/1.1
set / network-instance default interface ethernet-1/2.1
set / network-instance default protocols spanning-tree admin-state enable
commit now

## 注意点
- ハマりポイント1つだけ