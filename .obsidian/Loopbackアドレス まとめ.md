## 自分の最初の予想

最初の予想: 「LoopbackはポートじゃないからIPが落ちない。ホストからR1への疎通が取れなくなった場合、Loopbackにpingを飛ばしてR1自体は問題ない、つまりホストとR1の間の経路で問題が起きたという予想を立てられる」

→ **この予想は正しかった。ただしそれよりもっと深い役割がある。**

---

## Loopbackとは何か

物理インターフェースと違って**絶対にdownしない仮想インターフェース**。 ケーブルを抜いても、物理ポートが壊れても、Loopbackは生き続ける。 ルーティングプロトコルを動かすときに設定必要

```
interface Loopback0
 ip address 1.1.1.1 255.255.255.255
```

/32（ホストアドレス）で設定するのが慣例。

---

## なぜ実務で必須なのか

### 1. OSPFのRouter ID（今回一番重要）

OSPFはルーターを識別するために**Router ID**が必要。STPのルートブリッジ選出と似た概念。

**Router IDの決まり方（優先順位順）:**

1. 手動で設定した場合はその値
2. LoopbackのIPアドレスの中で一番大きい値
3. 物理インターフェースのIPアドレスの中で一番大きい値

**Loopbackがない場合の問題:**

- 物理IPがRouter IDになる
- そのインターフェースが落ちるとRouter IDが変わる
- Router IDが変わるとOSPFが再計算される
- 経路が不安定になる、予測できない動作をする

**Loopbackがある場合:**

- LoopbackのIPがRouter IDになる
- 物理リンクが何本落ちてもRouter IDは変わらない
- OSPFが安定して動く

### 2. 死活監視

監視システムがLoopbackのIPにpingを打つ。

```
物理ポートが1本落ちた場合:
- 物理PortのIPへのping → 通らない（そのポートが落ちてるから）
- LoopbackのIPへのping → 通る（別経路があれば）
```

つまり「ルーター自体が死んでるか」と「特定のリンクが落ちてるか」を区別できる。

### 3. BGPのピアリング（後で詳しくやる）

BGPで隣接関係を結ぶときにLoopbackのIPを使う。物理リンクが1本落ちても別経路でBGPセッションが維持される。

---

## STPとの比較で理解する

自分の気づき:「OSPFにもSTPと同じようにrootみたいなのがある」

||STP|OSPF|
|---|---|---|
|識別子|Bridge ID（Priority + MAC）|Router ID（Loopback IP）|
|選出ルール|Priorityが小さい方、同じならMACが小さい方|LoopbackIP > 物理IP（大きい方）|
|安定性|MACは変わらないから安定|LoopbackIPは落ちないから安定|
|手動指定|Priorityを下げる|Loopbackを設定する|

---

## 実務でのLoopback設定の慣例

```
R1のLoopback: 1.1.1.1/32
R2のLoopback: 2.2.2.2/32
R3のLoopback: 3.3.3.3/32
R4のLoopback: 4.4.4.4/32
```

覚えやすいアドレスにするのが慣例。/32はそのルーター自身だけを表すホストアドレス。

---

## 次のラボで実践すること

OSPF Multi Areaのラボで:

- 全ルーターにLoopbackを設定する
- Router IDがLoopbackのIPになってることを確認する（show ip ospf）
- 物理リンクを落としてもRouter IDが変わらないことを確認する