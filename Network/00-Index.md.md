### フェーズ1: 基礎固め（最重要）

1. IPv4 Subnetting
2. VLAN
3. Trunk/Access Port
4. Static Route
5. Default Route

### フェーズ2: Layer 2展開

6. STP
7. RSTP
8. EtherChannel/LACP
9. VLAN間ルーティング (Router-on-a-Stick)
10. SVI

### フェーズ3: Layer 3基礎

11. OSPF (Single Area)
12. OSPF (Multi Area)
13. Administrative Distance
14. Metric

### フェーズ4: 実務的機能

15. DHCP Server/Relay
16. Standard ACL
17. Extended ACL
18. NAT/PAT
19. SSH

### フェーズ5: 冗長化・可用性

20. HSRP
21. VRRP
22. PortFast/BPDU Guard

### フェーズ6: 高度なルーティング

23. EIGRP
24. BGP (eBGP)
25. BGP (iBGP)
26. Route Redistribution

### フェーズ7: セキュリティ

27. Port Security
28. DHCP Snooping
29. Dynamic ARP Inspection
30. 802.1X

### フェーズ8: 運用・管理

31. SNMP
32. Syslog
33. NTP
34. CDP/LLDP

### フェーズ9: 応用（必要に応じて）

35. QoS全般
36. GRE Tunnel
37. IPsec VPN
38. VRF
39. IPv6全般
40. Multicast

Network/
├── 00-Index.md
├── 01-Fundamentals/
│   ├── Subnetting.md
│   ├── VLAN.md
│   └── Static-Route.md
├── 02-Layer2/
│   ├── STP.md
│   ├── EtherChannel.md
│   └── VLAN-Routing.md
├── 03-Layer3/
│   ├── OSPF.md
│   ├── EIGRP.md
│   └── BGP.md
├── 04-Security/
├── 05-HA/
├── Labs/
│   ├── Lab01-Basic-VLAN.md
│   └── Lab02-OSPF-Single-Area.md
└── Troubleshooting/

# 機能名

## これは何？
一言で説明

## なぜ使う？
解決する問題

## 基本設定
````コード```

## 確認コマンド
```コマンド```

## 注意点
- ハマりポイント1つだけ

## 関連
[[リンク]]
```

---

## 具体例：VLAN
```markdown
# VLAN

## これは何？
LANを論理的に分割する機能

## なぜ使う？
- ブロードキャスト分離
- セキュリティ分離
- 部署ごとに分ける

## 基本設定
set / network-instance vlan10 type mac-vrf

## 確認コマンド
show network-instance

## 注意点
- VLAN IDは1-4094
- Trunkポート必須

## 関連
[[Trunk Port]] [[STP]]
```

---

## まとめ方のコツ

### やらないこと
- ❌ 教科書丸写し
- ❌ 完璧に書こうとする
- ❌ 体系的に整理
- ❌ 図を頑張って描く

### やること
- ✅ 自分の言葉で一言
- ✅ 詰まった点だけメモ
- ✅ 後から追記OK
- ✅ 箇条書きメイン

## 最小限ルール

1. **5分で書く** - 長くなったら削る
2. **完成させない** - 60%でOK
3. **コマンドはコピペ** - 説明だけ自分で
4. **詰まった点だけ** - 分かったことは書かない

## 例：ダメなノート vs 良いノート

### ❌ ダメ（詳しすぎ）
````
VLANとはVirtual Local Area Networkの略称で
IEEE 802.1Qで標準化されており...
歴史的背景としては...
（5ページ続く）
````

### ✅ 良い（必要最小限）
````
# VLAN
ネットワーク分割
VLAN 10 = 営業部
VLAN 20 = 開発部

詰まった：untaggedとtaggedの違い
→ accessポートはuntagged