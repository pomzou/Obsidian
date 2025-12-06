## これは何？
ルーティングテーブルに特定の宛先が見つからない場合に使用される経路

## なぜ使う？
ISP接続 
個別ルート不要のためルーティングテーブルの簡素化

## 基本設定
[PC] --- [R1] --- [R2] --- [Internet] 
LAN 　Gateway ISP
## Router1
configure terminal
hostname R1
interface GigabitEthernet2
 ip address 192.168.1.1 255.255.255.0
 no shutdown
interface GigabitEthernet3
 ip address 10.0.0.1 255.255.255.252
 no shutdown
exit
ip route 0.0.0.0 0.0.0.0 10.0.0.2
end
write memory

## Router2
configure terminal
hostname R2
interface GigabitEthernet2
 ip address 10.0.0.2 255.255.255.252
 no shutdown
interface Loopback0
 ip address 8.8.8.8 255.255.255.255
exit
ip route 192.168.1.0 255.255.255.0 10.0.0.1
end
write memory

## PC1
ip addr add 192.168.1.10/24 dev eth1 
ip link set eth1 up 
ip route add default via 192.168.1.1

## 確認コマンド

<font color="#c00000">R1#show ip route</font>
[Codes の一覧は省略]

Gateway of last resort is 10.0.0.2 to network 0.0.0.0

<font color="#c00000">S*    0.0.0.0/0 [1/0] via 10.0.0.2</font>
<font color="#c00000">      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks</font>
C        10.0.0.0/30 is directly connected, GigabitEthernet3
L        10.0.0.1/32 is directly connected, GigabitEthernet3
      192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.1.0/24 is directly connected, GigabitEthernet2
L        192.168.1.1/32 is directly connected, GigabitEthernet2
## → S* 0.0.0.0/0 と表示されればok

<font color="#c00000">R1#ping 10.0.0.2</font>
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/3 ms
## → pingが通ればok

<font color="#c00000">/ # ping 8.8.8.8</font>
PING 8.8.8.8 (8.8.8.8): 56 data bytes
64 bytes from 8.8.8.8: seq=0 ttl=115 time=4.427 ms
64 bytes from 8.8.8.8: seq=1 ttl=115 time=4.485 ms
64 bytes from 8.8.8.8: seq=2 ttl=115 time=5.129 ms
^C
--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 4.427/4.680/5.129 ms
## → pingが通ればok
## 注意点
- 最も優先度低い（AD=1）