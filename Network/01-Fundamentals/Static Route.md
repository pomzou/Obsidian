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
exit
ip route 10.1.1.0 255.255.255.0 10.1.2.1
end
write memory
## 確認コマンド
```コマンド```

## 注意点
- ハマりポイント1つだけ