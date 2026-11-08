# Maqueen v4/v5 Compat

Maqueen v4 向けの基本的な MakeCode API を、Maqueen v4 と Maqueen v5 の両方で使いやすくするための micro:bit MakeCode 互換拡張機能です。

この拡張機能は、公式 Maqueen 拡張とは別の `maqueenCompat` 名前空間を使います。公式 Maqueen 拡張を同じプロジェクトへ追加しても、TypeScript 上の `maqueen` 名前空間とは衝突しません。

## 使い方

MakeCode の「拡張機能」で、この GitHub リポジトリの URL を指定して追加します。

```text
https://github.com/Poseidon1024/pxt-maqueen
```

プログラムの最初で Maqueen を準備します。

```blocks
maqueenCompat.init(maqueenCompat.BoardVersion.Auto)
```

通常は `Auto` を使います。`init` で指定するのは、ラインセンサーの値の種類です。

| 指定 | 動き |
|---|---|
| `Auto`（自動） | v4・v5 のどちらでも v4 と同じ値で動かします |
| `V4` | v4 互換を明示します。v5 の実機でも v4 と同じ値で動かします（`Auto` と同じ動き） |
| `V5` | Maqueen v5 本来の値のまま動かします。ラインセンサーの値は公式 Maqueen v5 拡張と同じく黒が `1`、白が `0` になります |

実機のボード（v4 か v5 か）は自動で判別します。判別がうまくいかないときだけ、`setBoardVersion` でボードを明示してください。

```blocks
maqueenCompat.setBoardVersion(maqueenCompat.BoardVersion.V5)
maqueenCompat.init(maqueenCompat.BoardVersion.Auto)
```

| 指定 | 動き |
|---|---|
| `Auto`（自動） | 実機を判別します（初期状態） |
| `V4` | Maqueen v4 として、ピンでラインセンサーと LED を扱います |
| `V5` | Maqueen v5 として、I2C でラインセンサーと LED を扱います |

## ラインセンサー

`maqueenCompat.readPatrol(...)` は、`Auto` と `V4` では Maqueen v4 と Maqueen v5 のどちらでも v4 と同じ値にそろえて返します。

* `0`: 黒
* `1`: 白

`V5` を指定したときだけ、黒が `1`、白が `0` になります。ラインセンサーのイベント（白／黒のとき）は、どの指定でも色どおりに動きます。

Maqueen v5 では、左センサーと右センサーを v4 の左右センサーとして扱います。中央センサーはこの互換 API では使いません。

## 主な API

```blocks
maqueenCompat.init(maqueenCompat.BoardVersion.Auto)
maqueenCompat.motorRun(maqueenCompat.Motors.All, maqueenCompat.Dir.CW, 80)
maqueenCompat.motorStop(maqueenCompat.Motors.All)
maqueenCompat.readPatrol(maqueenCompat.Patrol.PatrolLeft)
maqueenCompat.Ultrasonic()
maqueenCompat.servoRun(maqueenCompat.Servos.S1, 90)
maqueenCompat.writeLED(maqueenCompat.LED.LEDLeft, maqueenCompat.LEDswitch.turnOn)
maqueenCompat.writeLED(maqueenCompat.LED.LEDAll, maqueenCompat.LEDswitch.turnOff)
```

## 例

```blocks
maqueenCompat.init(maqueenCompat.BoardVersion.Auto)

basic.forever(function () {
    let left = maqueenCompat.readPatrol(maqueenCompat.Patrol.PatrolLeft)
    let right = maqueenCompat.readPatrol(maqueenCompat.Patrol.PatrolRight)

    if (left == 0 && right == 0) {
        maqueenCompat.motorRun(maqueenCompat.Motors.All, maqueenCompat.Dir.CW, 80)
    } else if (left == 0) {
        maqueenCompat.motorRun(maqueenCompat.Motors.M1, maqueenCompat.Dir.CW, 30)
        maqueenCompat.motorRun(maqueenCompat.Motors.M2, maqueenCompat.Dir.CW, 90)
    } else if (right == 0) {
        maqueenCompat.motorRun(maqueenCompat.Motors.M1, maqueenCompat.Dir.CW, 90)
        maqueenCompat.motorRun(maqueenCompat.Motors.M2, maqueenCompat.Dir.CW, 30)
    } else {
        maqueenCompat.motorStop(maqueenCompat.Motors.All)
    }
})
```

## 対応範囲

この拡張機能は、Maqueen v4 の基本 API を Maqueen v5 でも使うための互換レイヤーです。モーター、ラインセンサー、超音波センサー、サーボ、LED ON/OFF などの v4 基本機能を対象にしています。Maqueen v5 固有の RGB 色指定、中央ラインセンサー、自動巡回、BLE などの機能をすべて提供するものではありません。
