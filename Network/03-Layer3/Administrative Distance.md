
## これは何？
ルーティングプロトコルの信頼度

## なぜ使う？
複数プロトコルから同じ宛先への経路がある場合の優先順位
### AD値 
- Connected: 0 
- Static: 1 
- EIGRP: 90 
- OSPF: 110
- RIP: 120

## 基本設定
[R1] --- [R2]

## Router1
configure terminal
hostname R1
interface GigabitEthernet2
 ip address 10.1.1.1 255.255.255.0
 no shutdown
exit
ip route 192.168.1.0 255.255.255.0 10.1.1.2
router ospf 1
 network 10.1.1.0 0.0.0.255 area 0
exit
end
write memory
## Router2
configure terminal
hostname R2
interface GigabitEthernet2
 ip address 10.1.1.2 255.255.255.0
 no shutdown
interface Loopback0
 ip address 192.168.1.1 255.255.255.0
exit
router ospf 1
 network 10.1.1.0 0.0.0.255 area 0
 network 192.168.1.0 0.0.0.255 area 0
exit
end
write memory

## 確認コマンド
```コマンド```

## 注意点
- AD小さい = 優先度高い 
- Static (1) > OSPF (110)
