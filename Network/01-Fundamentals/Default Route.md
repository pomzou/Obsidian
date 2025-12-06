## これは何？
ルーティングテーブルに特定の宛先が見つからない場合に使用される経路

## なぜ使う？
ISP接続 
個別ルート不要

## 基本設定
[PC] --- [R1] --- [R2] --- [Internet] 
LAN 　Gateway ISP

## 確認コマンド
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
## 注意点
- ハマりポイント1つだけ