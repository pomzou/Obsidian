## これは何？
動的ルーティングプロトコル 
自動で経路交換

## なぜ使う？
Static Routeの手動設定不要
障害時に自動切り替え

## 基本設定
[R1] --- [R2] --- [R3]
Area 0   Area 0   Area 0

## Router1
configure terminal
hostname R1
interface GigabitEthernet2
 ip address 10.1.1.1 255.255.255.0
 no shutdown
interface Loopback0
 ip address 1.1.1.1 255.255.255.255
exit
router ospf 1
 network 10.1.1.0 0.0.0.255 area 0
 network 1.1.1.1 0.0.0.0 area 0
exit
end
write memory
## Router2
configure terminal
hostname R2
interface GigabitEthernet2
 ip address 10.1.1.2 255.255.255.0
 no shutdown
interface GigabitEthernet3
 ip address 10.1.2.1 255.255.255.0
 no shutdown
interface Loopback0
 ip address 2.2.2.2 255.255.255.255
exit
router ospf 1
 network 10.1.1.0 0.0.0.255 area 0
 network 10.1.2.0 0.0.0.255 area 0
 network 2.2.2.2 0.0.0.0 area 0
exit
end
write memory
## Router3
configure terminal
hostname R3
interface GigabitEthernet2
 ip address 10.1.2.2 255.255.255.0
 no shutdown
interface Loopback0
 ip address 3.3.3.3 255.255.255.255
exit
router ospf 1
 network 10.1.2.0 0.0.0.255 area 0
 network 3.3.3.3 0.0.0.0 area 0
exit
end
write memory
## 確認コマンド
<font color="#c00000">R1#show ip ospf neighbor</font>

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   <font color="#c00000">FULL/DR </font>        00:00:32    10.1.1.2        GigabitEthernet2
## → 隣接ルータが `FULL` 状態ならOK

<font color="#c00000">R1#show ip route ospf</font>
[Codes の一覧は省略]

Gateway of last resort is not set

      2.0.0.0/32 is subnetted, 1 subnets
<font color="#c00000">O        2.2.2.2 [110/2] via 10.1.1.2, 00:00:43, GigabitEthernet2</font>
      3.0.0.0/32 is subnetted, 1 subnets
<font color="#c00000">O        3.3.3.3 [110/3] via 10.1.1.2, 00:00:43, GigabitEthernet2</font>
      10.0.0.0/8 is variably subnetted, 3 subnets, 2 masks
<font color="#c00000">O        10.1.2.0/24 [110/2] via 10.1.1.2, 00:00:43, GigabitEthernet2</font>
## → `O` で始まる経路（OSPFで学習した経路）があればOK

<font color="#c00000">R1#ping 3.3.3.3</font>
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 3.3.3.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/4 ms

## 注意点
- ワイルドカード = サブネットマスクの逆 
- area 0 = バックボーンエリア