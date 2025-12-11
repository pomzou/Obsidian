
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
<font color="#c00000">R1#show ip route 192.168.1.0</font>
Routing entry for 192.168.1.0/24
  <font color="#c00000">Known via "static", distance 1, metric 0</font>
  Routing Descriptor Blocks:
  * 10.1.1.2
      Route metric is 0, traffic share count is 1
## → Static (AD=1) が優先されているならOK
  
  <font color="#c00000">R1#show ip route</font>
[Codes の一覧は省略]

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        10.1.1.0/24 is directly connected, GigabitEthernet2
L        10.1.1.1/32 is directly connected, GigabitEthernet2
S     192.168.1.0/24 [1/0] via 10.1.1.2
## → Staticの経路があってOSPFの経路が表示されていないならOK

## 注意点
- AD小さい = 優先度高い 
- Static (1) > OSPF (110)
