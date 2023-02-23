# ゲームを作る

Pythonに慣れるにはいろいろとプログラムを書いてみるのが一番です。Pythonは機械学習やデータ処理の分野で人気がありますが、それ以外にもいろいろなことができます。本章の目的はゲームの作成を通してPythonに慣れることです。ゲーム用のライブラリはpygame, pyxel, pygame zeroなどいくつかありますが、今回は「Pygame Zero」を使用することにしました。プログラミング教育目的で設計されたこともあり、使い方がシンプルです。

## インストールして触ってみる
Pygame Zeroを使用するためにはモジュールをインストールする必要があります。詳細な使い方を説明するまえに、まずは触ってみましょう。

### インストール
コマンドプロンプト、もしくはターミナルから以下のコマンドを実行してください。

```
pip install pgzero
```
<word>
macOSの場合はpip3コマンドを使用してください。
</word>


最初のプログラムを実行してみましょう。
```python:pgz-window.py
import pgzrun                   ←(1)

WIDTH = 300                     ←(2)
HEIGHT = 300

def draw():                     ←(3)
    screen.fill((128, 0, 0))    ←(4)

pgzrun.go()
```
たったこれだけのコードでウインドウが表示されます。

最初に(1)で"import pgzrun"でモジュールを読み込みます。

次に(2)でグローバル変数WIDTH, HEIGHTに数値を代入することでウインドウのサイズを指定します。

(3)にあるように、draw関数を定義して描画する内容を記述します。この関数は描画が必要になったときに自動で呼び出されます。

(4)にあるscreenという変数はpgzeroにおいてウインドウを表すオブジェクトです。ここではfillメソッドで背景色(128,0,0)を指定しています。

赤・緑・青の3つの成分を組み合わせるといろいろな色を表現できるので、赤緑青は光の三原色と呼ばれたりします。Red, Green, Blueの頭文字をつなげるとRGBとなりますが、コンピュータの世界で色を表現するときによく使用されます。Pygame Zeroでも色は（赤成分, 緑成分, 青成分）のタプル形式で指定します。それぞれの成分は0 ~ 255の範囲で指定します。(128,0,0)は暗い感じの赤色という指定です。

最後にpgzrun.go()を実行すると処理が開始されます。

## 描画
ウインドウを表示するだけでは楽しくありません。そのウインドウ上にいろいろな描画をしてみましょう。まずは、基本図形から始めます。

### 基本的な図形の描画
screenオブジェクトにはdrawというプロパティがあります。そのオブジェクトには、描画のためのメソッドがいろいろ用意されています。

- draw.line(start, end, (r, g, b))
  - start の座標から end の座標まで直線を描画します。
- draw.circle(pos, radius, (r, g, b))
  - 中心がpos、半径がradiusの円の輪郭を描画します。
- draw.filled_circle(pos, radius, (r, g, b))
  - 中心がpos、半径がradiusの円を塗り潰します。
- draw.rect(rect, (r, g, b))
  - 四角形の輪郭を描画します。四角形の領域指定には Rect を使います。
- draw.filled_rect(rect, (r, g, b))
  - 塗り潰しで四角形を描画します。
- draw.text(text, pos)
  - テキストを描画します。
- draw.textbox(text, rect)
  - 引数に指定された Rect 領域の大きさでテキストを描画します。

```(r, g, b)```という部分は色指定です。

いくつかサンプルを見てみましょう
```python:basic-draw1.py
import pgzrun
WIDTH, HEIGHT = 300, 300

def draw():
    for i in range(0,300,10):       ←(1)
        screen.draw.line((0,i), (i,300), (i/2, 255, 255-i/2))   ←(2)

pgzrun.go()
```

実行結果は以下のようになります。

![](capture/draw1.png)


(1)でfor文を使い、0, 10, 20, ... 290まで30回繰り返しています。変数iはループ変数です。
(2)のlineメソッドは2つの座標を結ぶ線を描画します。引数のstart, endはそれぞれ座標で(x, y)の形式で指定します。上記例では、(0,i)から(i,300)へ直線を描画しています。



次は円と矩形の描画を見てみましょう。
```python:basic-draw2.py
import pgzrun
WIDTH, HEIGHT = 500, 450

def draw():
    for i in range(4):              ←(1)
        y = i*100 + 50
        x = i*100 + 100
        screen.draw.line((0,y), (WIDTH,y), (255,255,255))
        screen.draw.line((x,0), (x,HEIGHT), (255,255,255))

    for i in range(4):              ←(2)
        y = i*100 + 50
        c = (i*80, 128, 0)

        screen.draw.circle((100, y), 40, c)             ←(3)
        screen.draw.filled_circle((200, y), 40, c)      ←(4)
        r0 = Rect(300, y, 60, 40)
        r1 = Rect(400, y, 40, 60)
        screen.draw.rect(r0, c)                         ←(5)
        screen.draw.filled_rect(r1, c)                  ←(6)

pgzrun.go()
```

実行結果は以下のようになります。

![](capture/draw2.png)

最初のfor文(1)ではlineメソッドを使って座標の目印となる線を描画しています。次のfor文(2)では、左から右へ、
- circle(円)                    ... (3)
- filled_circle(塗り潰しの円)   ... (4)
- rect(矩形)                    ... (5)
- filled_rect(塗り潰しの矩形)   ... (6)

と描画しています。

円は中心の座標(x,y)、半径radiusで場所と大きさを指定します。

矩形の場所と大きさはpgzero.rect.Rectクラスのオブジェクトで指定します。

- Rect(x, y, w, h)
  - x:左上x座標、y:左上y座標、w:幅、h:高さ

それぞれの座標指定ですが、円は中心、矩形は左上頂点となっている点に注意してください。

テキストの描画をみてみましょう。
```python:basic-draw3.py
import pgzrun
WIDTH, HEIGHT = 700, 350

def draw():
    screen.draw.line((100, 0), (100, HEIGHT), (255, 255, 255))  ←(1)
    screen.draw.line((0, 100), (WIDTH, 100), (255, 255, 255))
    screen.draw.text("Hello", (100, 100), fontsize=50)          ←(2)

    screen.draw.text("Hello", (0, 200), fontsize=30)            ←(3)
    screen.draw.text("Hello", (100, 200), fontsize=50)
    screen.draw.text("Hello", (200, 200), fontsize=50, color=(0, 255, 0))
    screen.draw.text("Hello", (300, 200), fontsize=50,
                     color=(0, 255, 0), background=(200, 200, 200))

    for i in range(4):
        y = i * 50 + 50
        r = Rect(400, y, 100 + i*50, 40)
        screen.draw.rect(r, (255,255,255))
        screen.draw.textbox("World", r)                         ←(4)
pgzrun.go()
```

実行結果は以下のようになります。

![](capture/draw3.png)

(1)では(100,100)の座標で線が交わるようにlineメソッドを2回実行して線を描画します。
(2)ではその座標(100,100)を指定して、"Hello"という文字をfontsizeが50の大きさで文字を書いています。

(3)のtextメソッドは指定された座標に文字を描画します。
指定された座標は文字の左上になることに注意してください。fontsize, color, backgroundなどいろいろな指定ができます。画面中央部にサイズや色を変えて"Hello"と4つ描画しています。

(4)のtextboxメソッドは指定された矩形の中に文字を描画します。for文を使って、画面右にある縦に並んでいる矩形の中の文字を描画しています。

pygame zeroでのテキスト描画の詳細については以下のURLを参照してください。

https://pygame-zero.readthedocs.io/ja/latest/ptext.html

## イベント

Pygame zeroではプログラムが始まってから終了するまで、再描画、マウス、キーボード、タイマーなど、いろいろな事象が発生します。これらの事象のことをイベントとよびます。ここでは、イベントを処理する方法について見てゆきましょう。

### 描画イベント
- draw  = 画面の再描画が必要なときに呼び出されます。実際に画面に描画する内容はこの関数の中に記述します。

- update = 一定間隔で定期的に呼び出されます。デフォルトでは１秒間に60回実行されます。

以下はdraw, updateを使用したサンプルです。
```python:event1.py
import pgzrun
WIDTH, HEIGHT = 250, 150

count = 0

def draw():             ←(1)
    screen.clear()
    screen.draw.text(f"count={count}", (50,50), 
        color=(255,255,255), fontsize=50)

def update():           ←(2)
    global count
    count += 1

pgzrun.go()
```
(1)のdrawはcountの値を描画するだけです。
(2)のupdateはcountの値を1増やすだけです。

慣れないうちはdrawとupdateの使い分けに混乱するかもしれませんが、描画はdraw、その他の処理はupdateと考えるとよいでしょう。

### キーボード

キーが押下されたことを検出するにはon_key_down, on_key_up関数を定義します。

```python:event2.py
import pgzrun
WIDTH, HEIGHT = 450, 150

status = ""

def draw():
    screen.clear()
    screen.draw.text(f"{status}", 
        (50,50), color=(255,255,255), fontsize=50)

def on_key_down(key):       ←(1)
    global status
    status = f"on_key_down({key})"

def on_key_up(key):         ←(2)
    global status
    status = f"on_key_up({key})"

pgzrun.go()
```

キーが押下されると(1)のon_key_downが、離した時には(2)のon_key_upが呼び出されます。引数のkeyにはキーの番号が数値として渡されます。

このようにon_key_down, on_key_up関数を定義しておき、キーが押下されたときに関数を呼び出してもらうのは、電話の着信を待っているような*受動的*のような処理と考えることができます。

一方、*能動的*にキーの押下状態を検出することも可能です。詳しくはActorの節で説明します。


### マウス

マウスの押下、リリース、移動を検出するには以下の関数を定義します。

```python:event3.py
import pgzrun
WIDTH, HEIGHT = 550, 150

status = ""

def draw():
    screen.clear()
    screen.draw.text(f"{status}", 
        (50,50), color=(255,255,255), fontsize=50)

def on_mouse_down(pos):     ←(1)
    global status
    status = f"on_mouse_down({pos})"
def on_mouse_up(pos):       ←(2)
    global status
    status = f"on_mouse_up({pos})"
def on_mouse_move(pos):     ←(3)
    global status
    status = f"on_mouse_move({pos})"

pgzrun.go()
```

マウスが押されたら(1)のon_mouse_downが、
マウスが離されたら(2)のon_mouse_upが、
マウスが移動したら(3)のon_mouse_moveが呼び出されます。
それぞれ、マウスの座標がタプルの形式(X座標, Y座標)で引数に渡されます。

## Actor（画像）
ゲームを作るときに欠かせないのが画像です。ゲーム中の登場するキャラクターや背景などに画像を使用することが多いでしょう。そのような用途のためにActorクラスが用意されています。

### Actorを使った画像の描画
Pygame zeroで画像を表示する場合、実行ファイルがある場所にimagesというフォルダを作成して、その中に画像ファイルを格納します。画像ファイルの名前は全て小文字で指定する必要があることに注意してください。

まずは、画像を表示するだけのサンプルです。

```python:actor1.py
import pgzrun
WIDTH, HEIGHT = 500, 400

alien = Actor("alienpink", center=(250,200))

def draw():
    screen.clear()
    alien.draw()

pgzrun.go()
```

500x400のウインドウの中心にキャラクターが表示されます。

![](capture/actor1.png)

キャラクターを移動するには、そのx, yプロパティを更新します。以下は上下左右キーでキャラクターを移動するサンプルです。

```python:actor2.py
import pgzrun
WIDTH, HEIGHT = 500, 400

alien = Actor("alienpink", center=(250,200))

def draw():
    screen.clear()
    alien.draw()

def on_key_down(key):       ←(1)
    if key == keys.UP:
        alien.y -= 2
    if key == keys.DOWN:
        alien.y += 2
    if key == keys.LEFT:
        alien.x -= 2
    if key == keys.RIGHT:
        alien.x += 2
    
pgzrun.go()
```
実行するとわかりますが、キーを押しても少ししか移動しません。(1)のon_key_down関数はキーを1回押す度に1回だけしか呼び出されないためです。

スムーズに移動させたい場合は、以下のようにupdate関数の中でキーの押下判定を行います。

```python:actor3.py
import pgzrun
WIDTH, HEIGHT = 500, 400

alien = Actor("alienpink", center=(250,200))

def draw():
    screen.clear()
    alien.draw()

def update():        ←(1)
    if keyboard.UP:
        alien.y -= 2
    if keyboard.DOWN:
        alien.y += 2
    if keyboard.LEFT:
        alien.x -= 2
    if keyboard.RIGHT:
        alien.x += 2
    
pgzrun.go()
```
(1)のupdate関数は1秒間に60回呼び出されます。その都度、どのキーボードが押下されているか判定を行ってキャラクターを移動させているため、移動がスムーズになります。

### 衝突判定

キャラクターが衝突したときに何か処理をおこなう、そんなケースは少なくありません。Actorクラスには衝突判定用のメソッドが用意されているので簡単に実装できます。

- colliderect(Actor)
  - 別のActorオブジェクト衝突している場合Trueを返します。

- collidepoint((x,y))
  - 引数の座標がActorに含まれている場合Trueを返します。

以下は他のキャラクターとの衝突を検出するサンプルです。

```python:actor3.py
import pgzrun
from random import randint
WIDTH, HEIGHT = 500, 400

alien = Actor("alienpink", center=(250,200))
bat = Actor("bat")

def draw():
    screen.clear()
    alien.draw()
    bat.draw()

def update():
    if keyboard.UP:             ←(1)
        alien.y -= 2
    if keyboard.DOWN:
        alien.y += 2
    if keyboard.LEFT:
        alien.x -= 2
    if keyboard.RIGHT:
        alien.x += 2

    if alien.colliderect(bat):  ←(2)
        bat.x = randint(0, WIDTH)
        bat.y = randint(0, HEIGHT)

pgzrun.go()
```
(1)からのif文でキーが押されているか検出し、その結果に応じて上下左右キーでキャラクターを移動します。
(2)で衝突判定を行っています。コウモリにぶつかると、コウモリはランダムな場所に移動します。

![](capture/actor3.png)


### 回転
Actorは回転することも可能です。回転角は元の状態を0°とし、1回転が360度、反時計回りで指定します。

![](capture/actor4.png)

```python:actor4.py
import pgzrun
WIDTH, HEIGHT = 500, 400

alien = Actor("alienpink", center=(250,200))

def draw():
    screen.clear()
    alien.draw()

def update():
    if keyboard.RIGHT:
        alien.angle -= 2
    if keyboard.LEFT:
        alien.angle += 2
        
def on_mouse_move(pos):
    alien.angle = alien.angle_to(pos)   ←(1)

pgzrun.go()
```

以下は実行時の様子です。左矢印で反時計回りへ、右矢印で時計回りに回転します。

![](capture/actor5.png)

マウスを動かしても回転します。これは(1)において、マウス移動時にActorのangle_toメソッドを呼び出して、引数posへの方向を求め、その値を自身のangleプロパティに代入しているためです。

- Actor.angle_to(pos)
  - pos座標への回転角を返します。

実際に実行すると、"マウスの方向へ頭が向かない"と違和感を覚えるかもしれません。これは元の画像が上向き（0°の状態で上を向いている）のためです。マウスのほうへ頭が向くようにするには以下のように修正します。

```python
  def on_mouse_move(pos):
    alien.angle = alien.angle_to(pos)-90
```

### アニメーション

Actorのx, yを明示的に設定して座標を更新することもできますが、アニメーション関数を使用するとキャラクターを目的地までスムーズに移動できます。

- animate(actor, pos=(x, y))
  - 対象となるActorオブジェクト
  - x, y = 移動先の座標
  - さらにオプションで以下のような引数を指定できます。
    - duration = 何秒かけて移動するか（秒数）
    - tween = アニメーションの種類、liner, accelerate, decelerate, accel_decel, in_elastic, out_elastic, in_out_elastic, bounce_end, bounce_start, bounce_end_start のどれかを指定

アニメーションの効果（種類）は紙面では説明しづらいのでサンプルを作成してみました。以下のサンプルを実行してください。


```python:animation1.py
import pgzrun
WIDTH, HEIGHT = 500, 400

alien = Actor("alienpink", center=(0,200))

tweens = ["linear", "accelerate", "decelerate", "accel_decel", "in_elastic",    ←(1)
    "out_elastic", "in_out_elastic", "bounce_end", "bounce_start", "bounce_start_end"]

def draw():
    screen.clear()
    for i, t in enumerate(tweens):   ←(2)
        screen.draw.text(t, ((i%5)*100, (i//5)*30), fontsize=20)
    alien.draw()

def on_mouse_down(pos):
    x = min(pos[0] // 100, 4)
    y = min(pos[1] // 30, 1)
    t = tweens[y * 5 + x]
    animate(alien, pos=(450, 200), tween=t, duration=2, on_finished=animation_end)  ←(3)

def animation_end():                  ←(4)
    alien.x, alien.y = 0, 200

pgzrun.go()
```

画面上部の文字をクリックすると、そのアニメーションの種類が適用された状態でキャラクターが左から右へ移動します。

![](capture/animation1.png)

アニメーションは10種類あります。この例では、アニメーションの種類をリストtweens(1)で管理しています。

draw関数では、(2)のfor文を使ってリストの文字を描画しています。(i%5)で列、(i//5)で行の座標を計算しています。

マウスがクリックされるとon_mouse_down関数が呼び出されます。引数posにはクリック座標が格納されているので、その値からどの文字がクリックされたか求め、(3)のanimate関数を実行しています。

アニメーションの種類はtween引数で指定します。アニメーションが終了すると、on_finishedで指定した関数が呼び出されます。この例では(4)のanimation_end関数を呼び出して、キャラクターを元の座標に戻しています。

# サンプル

ここでは実際にゲームの例をいくつかご紹介します。いくつかサンプルを見ているとパターンが見えてくると思います。ぜひ自分でもいろいろなオリジナルゲームを作ってみてください。

## こうもりキャッチ

画面上部の緑色は残り時間です。コウモリをクリックするとスコアが増えます。

![](capture/tiny-bat.png)

### ソースコード

```python:tiny-bat.py
import pgzrun
from random import randint
WIDTH, HEIGHT = 500, 400

bat = Actor("bat", center=(250,200))
score = 0
time = WIDTH

def draw():         ←(1)
    screen.clear()
    screen.draw.filled_rect(Rect(0,0,time,10), (0,255,0))
    screen.draw.text(f"score:{score}", (50,50), color=(255,255,0), fontsize=40)
    if time < 0:    ←(2)
        screen.draw.text("GAME OVER", (120,200), color=(255,255,0), fontsize=60)
    bat.draw()

def update():
    global time
    time -= 0.5     ←(3)

def animation_end():    ←(4)
    if time > 0:        ←(5)
        animate(bat, pos=(randint(0,WIDTH), randint(0,HEIGHT)),
            on_finished=animation_end)

def on_mouse_down(pos):
    global score
    if time > 0 and bat.collidepoint(pos):  ←(6)
        score += 1

animate(bat, pos=(randint(0,WIDTH), randint(0,HEIGHT)),
        on_finished=animation_end)

pgzrun.go()
```

コウモリの移動にはアニメーションを使用しています。animate関数でランダムな位置へ移動しています。

主な関数は以下の通りです。

- 関数
    - draw (1)
        - 画面をクリアし、残り時間の矩形とスコアを描画します。(2)でtimeが0未満になるとGame Overと表示します。最後にbat.draw()でコウモリを描画しています。
    - update    
        - (3)で残り時間のtimeを減らしています
    - animation_end (4)
        - アニメーション終了時のコールバックです。(5)で残り時間がある場合は次の場所へ移動するためanimate関数を呼び出しています。
    - on_mouse_down
        - マウス押下時のコールバックです。(6)でtimeが0より大きく、マウスの座標がコウモリに含まれていたらscoreを増やします。


## ジャンプ
横スクロールゲームのサンプルです。キーの押下、マウスのクリックでジャンプします。敵をよけながらできるだけ長い距離を進んでください。

![](capture/tiny-jump.png)

### ソースコード

```python:tiny-jump.py
import pgzrun
from random import randint
WIDTH, HEIGHT = 1024, 512
back = Actor("bg")
score = 0
gameOver = False

class AnimateActor(Actor):                                  ←(1)
    def __init__(self, images, pos, speed):
        super().__init__(images[0], center=pos)
        self.images = images                                ←(2)
        self.index = 0                                      ←(3)
        self.speed = speed                                  ←(4)

    def move(self):                                         ←(5)
        self.index = (self.index + 1) % len(self.images)
        self.image = self.images[self.index]
        self.x += self.speed[0]
        self.y += self.speed[1]

alien = AnimateActor(images, pos=(150, 450), speed=[0, 0])  ←(6)
images = [f"p1_walk/p1_walk{i:02}" for i in range(1, 12)]

enemies = [                                                 ←(7)
    AnimateActor(["fish1", "fish2"], pos=[1300, 200], speed=[-5, 0]),
    AnimateActor(["snail1", "snail2"], pos=[800, 450], speed=[-2, 0]),
    AnimateActor(["spider1", "spider2"], pos=[1500, 450], speed=[-4, 0]),
    AnimateActor(["fly1", "fly2"], pos=[1200, 300], speed=[-8, 0]),
]

def draw():                 ←(8)
    back.draw()
    alien.draw()
    for e in enemies:
        e.draw()
    screen.draw.text(f"score:{score}", (50, 50),
                     color=(0, 0, 255), fontsize=60)
    if gameOver:            ←(9)
        screen.draw.text("GAME OVER", (250, 200),
                         color=(0, 0, 255), fontsize=120)

def update():               ←(10)
    global score, gameOver
    if gameOver:            ←(11)
        return

    score += 1
    for e in enemies:       ←(12)
        e.move()
        if e. x < 0:        ←(13)
            e.x = WIDTH + randint(0, 300)
        if e.colliderect(alien):        ←(14)
            gameOver = True

    alien.move()
    alien.speed[1] += 0.2               ←(15)
    if alien.y > 450:
        alien.speed[1] = 0
        alien.y = 450

    back.x -= 1                         ←(16)
    if back.x < 0:
        back.x = 1024

def jump():                             ←(17)
    if alien.y >= 450:
        alien.speed[1] = -12

def on_mouse_down(pos):
    jump()

def on_key_down(key):
    jump()

pgzrun.go()
```

アニメーションのように画像を切り替えて表示するように、AnimateActorクラスを実装しました。Actorクラスを継承しているため、Actorの機能はすべて使用できます。

- AnimateActorクラス　(1)
    - プロパティ
        - images = 画像のリスト                 (2)
        - index = 何枚目の画像を表示するか      (3)
        - speed = 移動する速度（x, y）          (4)
    - メソッド
        - move = 画像を切り替えながらspeed分移動します。    (5)

主な関数は以下の通りです。

- 関数
    - グローバルコード
        - (6)で主人公alienを作成します。敵は、AnimateActorオブジェクトを含むenemiesリスト(7)として管理しています。
    - draw  (8)
        - 背景と主人公、敵、スコアを描画します。(9)でゲームオーバー時にはGAME OVERと描画します。
    - update    (10)
        - gameOverがTrueのときは(11)で、すぐにreturnします。(12)のfor文で敵を取り出して移動させます。(13)で、画面の左外に出た場合には、x座標を更新して画面右外に移動します。(14)で、主人公alienと衝突したらgameOverをTrueにしています。
        主人公をmoveで移動しますが、落下方向の加速度を加えるために、(15)でspeed[1]を0.2増やしています。y座標が450を超えたらy方向のスピードを0にしています。最後に(16)で背景画像をスクロールしています。
    - jump  (17)
        - ジャンプするよう主人公のy方向の速度に-12を設定しています。


## ブロック崩し

ボールを反射させてブロックを崩してゆくゲームです。

![](capture/tiny-blocks.png)

### ソースコード
```python:tiny-blocks.py
import pgzrun
from math import radians, sin, cos
from random import randrange
WIDTH, HEIGHT = 600, 700

class Block(Rect):                          ←(1)
    def __init__(self, x, y, c, p):
        super().__init__(x, y, 80, 20)
        self.color = c                      ←(2)
        self.point = p                      ←(3)
        self.erase = False                  ←(4)

score = 0
ball = [300, 200]
angle = randrange(90-45, 90+45)
speed = 5
cols = ["red", "orange", "yellow", "blue"]
paddle = Block(300, 650, "yellow", 0)       ←(5)

blocks = []                                 
for i, y in enumerate(range(4)):            ←(6)
    for x in range(6):
        blocks.append(Block(x*100+10, y*30+60, cols[i], (5-i)*20))

def draw():                                 ←(7)
    screen.clear()
    screen.draw.text(f"score:{score}", (30, 10), fontsize=30, color="yellow")
    screen.draw.filled_rect(paddle, paddle.color)
    for b in blocks:                        ←(8)
        screen.draw.filled_rect(b, b.color)
    if len(blocks) == 0:                    ←(9)
        screen.draw.text("CLEAR!!!", (160, 300), fontsize=100, color="yellow")
        return
    if ball[1] > HEIGHT:                    ←(10)
        screen.draw.text("Game Over!!!", (160, 300), fontsize=70, color="yellow")

    screen.draw.filled_circle(ball, 10, color="yellow")

def update():                               ←(11)
    global angle, blocks, score, speed
    ball[0] += speed * cos(radians(angle))  ←(12)
    ball[1] += speed * sin(radians(angle))
    if ball[0] < 0 or ball[0] > WIDTH:        ←(13)
        angle = 180 - angle
    if ball[1] < 0:                         ←(14)
        angle = -angle
        speed = 10

    if paddle.collidepoint(ball):           ←(15)
        r = (ball[0] - paddle.center[0]) / paddle.width  # r = -0.5 ~ +0.5
        angle = -90 + 60 * r

    for b in blocks:                        ←(16)
        if b.collidepoint(ball):
            b.erase = True
            score += b.point
            angle = -angle
            break

    blocks = [b for b in blocks if not b.erase]

def on_mouse_move(pos):
    paddle.center = (pos[0], 650)

pgzrun.go()
```

(1)でブロックとパドルを管理するためにRectクラスを継承したBlockクラスを定義しています。
色、スコアに加算する点数、画面から消えたかどうかというプロパティを追加しています。

- Blockクラス   (1)
    - プロパティ
        - color = ブロックの色              (2)
        - point = 壊したときに加算する点数  (3)
        - erase = 画面から消えるか否か      (4)


- 関数
    - グローバルコード
        - ブロックはBlockオブジェクトとして実装しています。パドルもBlockオブジェクトです。(5)で作成して、広域変数paddleに代入しています。(6)では2重forループを使って、ブロックを作成し、リストblocksに追加しています。
    - draw              (7)
        - 画面をクリアし、スコアとパドルを描画します。(8)のfor文でブロックを取り出し描画します。(8)では、blocksが空になったらCLEAR、(10)では、ボールのY座標がHEIGHTを超えるとGAMEOVERのメッセージを表示します。最後にボールを描画しています。
    - update            (11)
        - (12)では、ボールの進行方向angle、速度speedから、三角関数のcos, sinを使用して、x方向とy方向の移動量を計算し、ボールを移動します。(13)は左右の壁に衝突時の判定です。180-angleという式で反射角を更新します。(14)は上の壁に衝突したときの処理です。角度の符号を-1倍して反射しています。
        (15)はパドルに衝突したときの処理です。パドルの中心からどの程度の割合で離れているか(-0.5から+0.5)計算し、それに応じて反射角を調整しています。
        (16)では、for文でブロックを取り出し、ボールと衝突しているか判定し、衝突したときはeraseプロパティをTrueに変更し、スコアを加算し、ボールの角度angleを反転させて向きを変えています。最後にリスト内包表記をしようして、eraseがTrueになっているブロックを削除しています。

## Fruits Cutter

飛び出してくるフルーツをマウスで切るゲームです。爆弾を切ってしまうとスコアが0にリセットされてしまうので注意してください。

![](capture/tiny-fruits.png)

### ソースコード
```python:tiny-fruits.py
import pgzrun
from random import randint

WIDTH = 500
HEIGHT = 400

class Fruit(Actor):                     ←(1)
    def __init__(self, file, bomb):
        super().__init__(file)
        self.bomb = bomb                ←(2)
        self.setup()

    def setup(self):                    ←(3)
        self.x = randint(50,WIDTH-100)  ←(4)
        self.y = HEIGHT
        self.sx = randint(-2, 2)        ←(5)
        self.sy = randint(-10, -5)
        self.time = randint(50, 100)     ←(6)

    def move(self):                     ←(7)
        self.x += self.sx
        self.y += self.sy
        self.sy += 0.1

fruits = []
mouse, pmouse = None, None
score = 0
frameCount = 0
flash = False
timeUp = False
isDown = False

for i in range(3):                              ←(8)
    fruits.append(Fruit(f"fruit{i}", i == 0))

def draw():
    global flash
    screen.draw.filled_rect(Rect((0,0), (frameCount/2, 5)), (0,0,255))          ←(9)
    screen.draw.text(f"Score:{score}", (20,40), color="yellow", fontsize=40)

    if timeUp:                                                                  ←(10)
        screen.draw.text(f"Time UP", (200,200), color="yellow", fontsize=40)
        return

    for f in fruits:                                                            ←(11)
        if f.time < frameCount:
            f.move()
            f.draw()

        if f.y > HEIGHT * 2:                                                    ←(12)
            f.time = frameCount + randint(0,100)
            f.setup()

    if flash:                                                                   ←(13)
        screen.draw.filled_rect(Rect((0,0), (WIDTH, HEIGHT)), (255,255,0))
        flash = False

    if mouse and pmouse:
        screen.draw.line(pmouse, mouse, (255,255,255))
    
def update():       ←(14)
    global frameCount, timeUp
    frameCount += 1
    timeUp = frameCount/2 > WIDTH
    screen.blit("black500x400",(0,0))       ←(15)

def on_mouse_down(pos):
    global isDown, pmouse, mouse
    isDown, pmouse, mouse = True, pos, pos

def on_mouse_up():
    global isDown, pmouse, mouse
    isDown, pmouse, mouse = False, None, None

def on_mouse_move(pos):
    global score, pmouse, mouse, flash
    if isDown:
        pmouse = mouse
        mouse = pos
        for f in fruits:
            if f.collidepoint(pos):
                if f.bomb:
                    score = 0
                    flash = True
                else:
                    score += 1
                f.setup()

pgzrun.go()
```

フルーツと爆弾を管理するためにActorクラスを継承したFruitクラスを定義しました。爆弾か否かをbombプロパティで、座標をxとyプロパティで、速度をsxとsyプロパティで、投げる時刻をtimeプロパティで管理しています。

- Fruitクラス           (1)
    - プロパティ
        - bomb = 爆弾か否か     (2)
        - x,y = 座標            (4)
        - sx,sy = 速度          (5)
        - time = 投げる時刻     (6)
    - メソッド
        - setup = 位置・座標・投げる時刻を初期化します      (3)
        - move = 座標を更新し、y方向の加速度を増やします    (7)


- 関数
    - グローバルコード
        - (8)では、個々のフルーツをFruitオブジェクトとして作成し、リストfruitsに追加します。
    - draw
        - (9)では、残り時間を画面上部に、スコアを描画します。(10)では、時間切れのときにTime Upと描画してreturnします。(11)では、fruitsリストから個々のFruitオブジェクトを取り出し、投げる時刻を過ぎていたらmoveメソッドで移動し、drawメソッドで描画します。(12)では、落下して画面の下を過ぎたら、次に投げる時刻を設定しています。(13)では、flashフラグがTrueの時は画面を黄色で塗りつぶし、画面が点滅する効果を演出しています。
    - update    (14)
        - frameCount変数を更新し、時間切れかどうかのフラグをセットします。(15)では、残像の効果を表現するため半透明の黒色の画像を描画しています。
    - on_mouse_down, on_mouse_up
        - マウスの押下、リリースに応じて変数を更新します。
    - on_mouse_move
        - マウスが押下されているときフルーツと座標が交差しているか調べます。衝突対象が爆弾の時はスコアを0にして、flashフラグをTrueにします。

## インベーダー
サンプルフォルダにインベーダーのゲームも格納してみました。
