## これは何？
複数エリアに分割したOSPF

## なぜ使う？
大規模ネットワークで負荷分散 
Area 0が必須（バックボーン）

## 基本設定
[R1] --- [R2] --- [R3] --- [R4]
Area 1   Area 0   Area 0   Area 2
　　　   (ABR)            　  (ABR)
　　　　
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
 network 10.1.1.0 0.0.0.255 area 1
 network 1.1.1.1 0.0.0.0 area 1
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
 network 10.1.1.0 0.0.0.255 area 1
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
interface GigabitEthernet3
 ip address 10.1.3.1 255.255.255.0
 no shutdown
interface Loopback0
 ip address 3.3.3.3 255.255.255.255
exit
router ospf 1
 network 10.1.2.0 0.0.0.255 area 0
 network 10.1.3.0 0.0.0.255 area 2
 network 3.3.3.3 0.0.0.0 area 0
exit
end
write memory
## Router4
configure terminal
hostname R4
interface GigabitEthernet2
 ip address 10.1.3.2 255.255.255.0
 no shutdown
interface Loopback0
 ip address 4.4.4.4 255.255.255.255
exit
router ospf 1
 network 10.1.3.0 0.0.0.255 area 2
 network 4.4.4.4 0.0.0.0 area 2
exit
end
write memory

## 確認コマンド
<font color="#c00000">R1#show ip ospf neighbor</font>

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   <font color="#c00000">FULL/DR</font>         00:00:34    10.1.1.2        GigabitEthernet2
## → 隣接ルータが `FULL` 状態ならOK

<font color="#c00000">R1#show ip route ospf</font>
[Codes の一覧は省略]

Gateway of last resort is not set

      2.0.0.0/32 is subnetted, 1 subnets
<font color="#c00000">O IA     2.2.2.2 [110/2] via 10.1.1.2, 00:00:23, GigabitEthernet2</font>
      3.0.0.0/32 is subnetted, 1 subnets
<font color="#c00000">O IA     3.3.3.3 [110/3] via 10.1.1.2, 00:00:18, GigabitEthernet2</font>
      4.0.0.0/32 is subnetted, 1 subnets
<font color="#c00000">O IA     4.4.4.4 [110/4] via 10.1.1.2, 00:00:13, GigabitEthernet2</font>
      10.0.0.0/8 is variably subnetted, 4 subnets, 2 masks
<font color="#c00000">O IA     10.1.2.0/24 [110/2] via 10.1.1.2, 00:00:23, GigabitEthernet2</font>
<font color="#c00000">O IA     10.1.3.0/24 [110/3] via 10.1.1.2, 00:00:18, GigabitEthernet2</font>
## → `O IA`（Inter-Area）の経路があればOK

<font color="#c00000">R1#ping 4.4.4.4</font>
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 4.4.4.4, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
## → pingが通ればOK
## 注意点
- Area 0は必須 
- 全エリアはArea 0経由で通信