# GameCapsuleJS

[![npm](https://img.shields.io/npm/v/game-capsule.svg)](https://www.npmjs.com/package/game-capsule)
[![npm](https://img.shields.io/npm/dt/game-capsule.svg)](https://npmcharts.com/compare/game-capsule?minimal=true)
[![npm](https://img.shields.io/npm/l/game-capsule.svg)](https://www.npmjs.com/package/game-capsule)

create.js に依存したゲーム作成用フレームワーク  
ゲームのメイン処理の作成に集中できるよう、その他気にしないといけないことはこれがやってくれる

# やってくれること

- ウィンドウサイズの canvas を生成
- リサイズイベントに合わせて画面幅をリサイズ
- Retina 対応
- ゲームの開始、停止、再開、終了する機能
- デバイスチェック
- ランダムな値生成関数
- カウントダウン

# example

- [Getting Started](https://yuki-sakaguchi.github.io/GameCapsuleJS/example/getting_started/index.html)
- [es2015](https://yuki-sakaguchi.github.io/GameCapsuleJS/example/es2015/index.html)
- [es5](https://yuki-sakaguchi.github.io/GameCapsuleJS/example/es5/index.html)

# CDN

```
https://unpkg.com/game-capsule@latest/lib/game-capsule.min.js
```

# 使い方

## 準備

`canvas`要素を定義し、必要なファイルを読み込む

```
<!-- デフォルトではcanvas[id="canvas"]が対象 -->
<canvas id="canvas"></canvas>

<!-- 依存しているcreate.jsとgame-capsule.jsを読み込む -->
<script src="https://code.createjs.com/1.0.0/createjs.min.js"></script>
<script src="js/game-capsule.js"></script>
```

## 実装

### ES2015

1. `GameCapsule`クラスを extends したクラスを定義する
2. 定義したクラスに`init`メソッド(canvas 上で使う変数の定義)と`update`メソッド(fps に合わせた stage の更新処理)を定義する

```
/**
 * GameCapsuleを継承したクラスを定義
 */
class Game extends GameCapsule {
  constructor (options) {
    super(options)
  }

  /**
   *  stageで使う変数などを定義
   */
  init () {
    let isRight = true
    let movePoint = 1

    // このスコープで使うだけのものはlet, constで良い
    let shape = new createjs.Shape()
    shape.graphics.beginFill('DarkRed').drawCircle(0, 0, 30)
    shape.x = this.width / 2
    shape.y = this.height / 2
    this.stage.addChild(shape)

    // updateで使いたい変数や関数はthisで定義する
    this.move = () => {
      shape.x += isRight ? movePoint : -movePoint
      if (shape.x > this.width || shape.x < 0) {
        isRight = !isRight
      }
    }
  }

  update () {
    this.move()
  }
}

// インスタンスを作成する
const game = new Game({
  target: '#canvas',
  isRetina: true,
  isTouch: true,
  parent: '#parent',
  countDownSeconds: 30
})
```

### ES5

1. `GameCapsule`クラスでインスタンスを生成する
2. 生成されたインスタンスに`init`メソッド(canvas 上で使う変数の定義)と`update`メソッド(fps に合わせた stage の更新処理)を定義する

```
/**
  * まずはインスタンス生成
  */
// オプションで色々設定できる
var game = new GameCapsule({
  target: '#canvas',
  isRetina: true,
  isTouch: true,
  parent: '#parent',
  countDownSeconds: 30
})

/**
  *  stageで使う変数などを定義
  */
game.init = function() {
  var _this = this
  var isRight = true
  var movePoint = 1

  // このスコープで使うだけのものはvarで良い
  var shape = new createjs.Shape()
  shape.graphics.beginFill('DarkRed').drawCircle(0, 0, 30)
  shape.x = this.width / 2
  shape.y = this.height / 2
  this.stage.addChild(shape)

  // updateで使いたい変数や関数はthisで定義する
  this.move = function() {
    shape.x += isRight ? movePoint : -movePoint
    if (shape.x > _this.width || shape.x < 0) {
      isRight = !isRight
    }
  }
}

/**
  * メインループ（stage.update()は自動でやるので不要）
  */
game.update = function(e) {
  this.move()
}
```

## 実行

1. `play`メソッドを実行すると動きだす
2. 動きを止めたいときは`pause`メソッドをを実行。止まっている時に再び`pause`メソッドを実行すると動きだす
3. 全てを初期化して初めから動かし直したいときは`reset`メソッドを実行

```
window.addEventListener('load', function() {
  game.play() // load後自動で再生

  // ポーズボタンで動きを止める
  document.querySelector('.js-pause').addEventListener('click', function() {
    game.pause()
  });

  // リセットボタンでリセット
  document.querySelector('.js-reset').addEventListener('click', function() {
    game.reset()
  })
})
```

# 使い方詳細

## オプション

GameCapsule インスタンスを生成する際に渡せるオプション

### target

対象の canvas  
デフォルトは`#canvas`

### isRetina

window.devicePixelRatio に応じて canvas や stage の倍率を変えるかどうか  
これが true だと Retina の端末で画像がボケたりしない  
デフォルトは`true`

### isTouch

stage をタッチイベントをつけるかどうか  
デフォルトは`true`

### parent

canvas の大きさを決める親要素  
ここに指定した要素を元に、canvas の大きさを決める  
親の指定がなければブラウザ幅を元にする  
デフォルトは`null`

### countDownSeconds

ゲーム開始から終了までのカウントダウンをする場合に使う  
設定できる単位は秒  
設定した秒数が経過した後に`isCountDownComplete`メソッドを実行すると true が帰ってくる  
デフォルトは`0`（カウントダウンしない）

## プロパティ

GameCapsule インスタンスが持つプロパティ

### mouseX

stage.mouseX の値  
isRetina が true の時に使うと Retina に対応した値が取れる

```
gameCapsule.mouseX;
```

### mouseY

stage.mouseY の値  
isRetina が true の時に使うと Retina に対応した値が取れる

```
gameCapsule.mouseY;
```

### width

canvas.width の値  
isRetina が true の時に使うと Retina に対応した値が取れる

```
gameCapsule.width;
```

### height

canvas.height の値  
isRetina が true の時に使うと Retina に対応した値が取れる

```
gameCapsule.height;
```

### isIos

iPad, iPhone, iPod の場合`true`, それ以外`false`

```
gameCapsule.isIos; //-> true or false
```

### isAndroid

Android 端末の場合`true`, それ以外`false`

```
gameCapsule.isAndroid; //-> true or false
```

### isMobile

iPad, iPhone, iPod, Android のいずれかの場合`true`, それ以外`false`

```
gameCapsule.isMobile; //-> true or false
```

### isPause

ゲームがポーズ状態の場合`true`, それ以外`false`

```
gameCapsule.isPause; //-> true or false
```

### totalTime

play 関数を実行してからの時間を取得できる

```
gameCapsule.totalTime; //-> {ms: 17260, s: 172, m: 2}
```

## メソッド

GameCapsule インスタンスが使えるメソッド

### play

init 関数実行後、update 処理を開始

```
gameCapsule.play();
```

### pause

update 処理を止める  
update 処理が止まっている状態で実行すると再び動き出す

```
gameCapsule.pause();
```

### reset

stage を全て初期化して、play 関数を実行し直す

```
gameCapsule.reset();
```

### getDispTime

totalTime を元に表示用に整形した値を取得  
引数に true を入れるとゼロ詰めされた状態で取得

```
gameCapsule.getDispTime(); //-> {ms: 9, s: 16, m: 5}
gameCapsule.getDispTime(true); //-> {ms: "09", s: "16", m: "05"}
```

### isCountDownComplete

オプションで`countDownSeconds`を設定しているときに使うことができる  
設定した秒数が過ぎてからこのメソッドを実行すると`true`が返される  
時間が過ぎていない場合、または countDownSeconds を設定していない場合は`false`が返される

```
gameCapsule.isCountDownComplete() //-> true or false
```

### divisionRetina

引数の値を window.devicePixelRatio で割って返す  
isRetina が true の場合、stage と canvas がブラウザによって倍率が変わってしまうので、そこらへんを揃えるために使う

```
// window.devicePixelRatio -> 2の場合
gameCapsule.divisionRetina(100); //-> 50
```

### createRandom

第１引数と第２引数の間の整数をランダムで生成する

```
gameCapsule.createRandom(1, 10); //-> 1〜10のどれかの整数
```

### zeroPadding

数字のゼロ埋め  
第１引数にターゲットの数値、第２引数には桁数を入れる

```
gameCapsule.zeroPadding(33, 5); //-> 00033
```

### Gacha

ガチャガチャコンストラクタが使えます  
ランダムで要素を取り出す時などに便利です

```
// まずはガチャの中身を作る
var list = [
  {
    name: 'スライム',
    par: 90
  },
  {
    name: 'はぐれメタル',
    par: 10
  }
]

// 第１引数にガチャの中身、第２引数にはパーセンテージを扱うプロパティ名を渡して、インスタンスを作成
var gacha = new gameCapsule.Gacha(list, 'par')

// 設定に応じてランダムでガチャの中身を取り出す
gacha.draw() //-> { name: 'スライム', par: 90 }
```
