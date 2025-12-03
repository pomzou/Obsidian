## これは何？
手動でほかのネットワークの経路を設定して認識させる。

## なぜ使う？
小規模なネットワーク構成の際に動的ルーティングが不必要な際に使用する。
設定がシンプルでルーター負荷が少ない

## 基本設定
[Router1] --- [Router2] --- [Router3]
10.1.1.0/24 　10.1.2.0/24 　10.1.3.0/24
## Router1
configure terminal
interface GigabitEthernet2
 ip address 10.1.1.1 255.255.255.0
 no shutdown
exit
ip route 10.1.2.0 255.255.255.0 10.1.1.2
ip route 10.1.3.0 255.255.255.0 10.1.1.2
end
write memory
## Router2
configure terminal
interface GigabitEthernet2
 ip address 10.1.1.2 255.255.255.0
 no shutdown
interface GigabitEthernet3
 ip address 10.1.2.1 255.255.255.0
 no shutdown
exit
ip route 10.1.3.0 255.255.255.0 10.1.2.2
end
write memory
## Router3
configure terminal
interface GigabitEthernet2
 ip address 10.1.2.2 255.255.255.0
 no shutdown
 interface Loopback0
 ip address 10.1.3.1 255.255.255.0
exit
ip route 0.0.0.0 0.0.0.0 10.1.2.1
end
write memory
## 確認コマンド
<font color="#c00000">router1#show ip route</font>
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override, p - overrides from PfR

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 4 subnets, 2 masks
C        10.1.1.0/24 is directly connected, GigabitEthernet2
L        10.1.1.1/32 is directly connected, GigabitEthernet2
S        10.1.2.0/24 [1/0] via 10.1.1.2
S        10.1.3.0/24 [1/0] via 10.1.1.2

<font color="#c00000">router1#show ip interface brief</font>
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet1       10.0.0.15       YES TFTP   up                    up      
GigabitEthernet2       10.1.1.1        YES manual up                    up 

<font color="#c00000">router1#ping 10.1.2.1</font>
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.2.1, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/2 ms
<font color="#c00000">router1#ping 10.1.2.2</font>
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.2.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
<font color="#c00000">router1#ping 10.1.3.1</font>
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.3.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
→ vlan10, vlan20が表示されればOK

## 注意点
- ハマりポイント1つだけ