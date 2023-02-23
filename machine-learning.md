# 機械学習
Pythonがここまで人気になったのは機械学習関連のモジュールやライブラリの充実があったからといえるでしょう。機械学習に関して掘り下げるとそれだけで書籍何冊分ものボリュームになってしまいます。ここでは「過去の使用電力のデータから、未来の電力利用料を予想する」、そんな作業を通して機械学習の雰囲気に触れていただければと思います。

## 準備

機械学習にはいろいろなライブラリがありますが、ここでは以下のライブラリを使用します。
- データ処理用にpandas
- 機械学習用にscikit learn
- グラフの描画にmatplotlib

いずれも機械学習入門とてもよく利用されるライブラリです。

### モジュールのインストール

コマンドプロンプトで（macOSではターミナルで）以下のコマンドを実行し、必要なモジュールをインストールしてください。
```
pip install pandas
pip install scikit-learn
pip install matplotlib
```

### CSVファイルのダウンロード

- 東京電力
東京電力の過去の実績データは以下のURLからダウンロードできます。

https://www.tepco.co.jp/forecast/html/download-j.html


今回は2022年分をダウンロードし、dataとフォルダの下にjuyo-2022.csvとして保存しました。

- 気象庁
2022年の天候データをダウンロードし、dataフォルダの下にondo-2022.csvとして保存しました。

https://www.data.jma.go.jp/gmd/risk/obsdl/

いろいろな地点のデータをダウンロードできますが、東京を選択しました。

## 電力データの読込み
まずは消費電力のCSVファイルを読み込んで、その内容をグラフで描画してみましょう。

### CSVファイルの読込み
CSVファイルは以下のように、それぞれの日付・時刻の消費電力量が格納されています。最初に３行分のヘッダ領域があることがわかります。

![](capture/ml1.png)

最初の3行はスキップし、A列とC列を取り込みます。

Pandasのread_csv関数を使用すると、CSVファイルからDataFrameというオブジェクトを作成できます。DataFrameは表形式のデータを集計・管理する便利なオブジェクトです。

```python:ml1.py
import pandas as pd                                 ←(1)
df = pd.read_csv("data/juyo-2022.csv",              ←(2)
    encoding="shift_jis",
    skiprows=3, parse_dates=[0], names=["date", "kw"], 
    usecols=[0, 2])
print(df.head())                                    ←(3)
```

まず(1)でライブラリを読込み、別名(エイリアス)pdと名付けています。多くの開発者がこのようにpdという別名をつけているようです、

(2)でCSVファイルを読み込みます。read_csvには様々な引数が用意されています。今回使用した引数は以下の通りです。
- "data/juyo-2022.csv" ... CSVファイルへのパス
- encoding="shift_jis" ... csvファイルのエンコードがShift_JISになっているのでencoding引数で"shift_jis"を指定
- skiprows=3 ... CSVファイルには最初の３行データ以外の内容が含まれているのでスキップ
- usecols=[0, 2] ... 日と使用量kwの列がほしいので[0,2]と指定
- names=["date", "kw"] ... 列名を指定

read_csv関数は、戻り値としてデータフレームオブジェクトを返します。データフレーム(DataFrame)は表形式でデータを管理する多機能なオブジェクトです。

(3)のheadはデータフレームの先頭を返すメソッドです。実行すると以下のようにCSVの先頭が表示されます。
```
        date    kw
0 2022-01-01  3266
1 2022-01-01  3062
2 2022-01-01  2929
3 2022-01-01  2828
4 2022-01-01  2786
```

元データでは各時間ごとのデータが格納されていました。すなわち1日あたり24行のデータがあったことになります。日毎の電力消費量を求めるためにDataFrameオブジェクトで日毎に集計する必要があります。

```python:ml2.py
import pandas as pd
df = pd.read_csv("data/juyo-2022.csv", 
    encoding="shift_jis",
    skiprows=3, parse_dates=[0], names=["date", "kw"], 
    usecols=[0, 2])

df_juyo = df.groupby("date").sum()                  ←(1)
print(df_juyo.head())
```
(1)では、データフレームdfのgroupbyメソッドを実行して日毎に集計を行い、さらにその戻り値に対してsumメソッドを実行して合計値を求めています。

出力は以下の通りです。各日の合計利用量が出力されています。

```
               kw
date
2022-01-01  74952
2022-01-02  75147
2022-01-03  74114
2022-01-04  81826
2022-01-05  92763
```

### 電力使用量グラフの描画
matplotlibモジュールを使用するとグラフを描画できます。

```python:ml3.py
import pandas as pd
import matplotlib.pyplot as plt                     ←(1)
df = pd.read_csv("data/juyo-2022.csv", 
    encoding="shift_jis",
    skiprows=3, parse_dates=[0], names=["date", "kw"], 
    usecols=[0, 2])

df_juyo = df.groupby("date").sum()
plt.plot(df_juyo)                                   ←(2)
plt.show()                                          ←(3)
```

![](capture/ml-graph1.png)

夏と冬に電力消費量が高くなっていることが分かります。

(1)ではmatplotlibのpyplotモジュールをpltという名前でインポートしています。(2)にあるように、plot関数にDataFrameを渡すとグラフが描画されます。(3)で実際にグラフを見える状態にしています。

## 気象データの読込み

電力消費量と気温はおそらく関係があると思われます。同じように気象庁からダウンロードしたCSVファイルをグラフに描画してみましょう。

### CSVファイルの読込み

電力のCSVと同じようにDataFrameに読み込みます。

```python:ml4.py
import pandas as pd
df_ondo = pd.read_csv("data/ondo-2022.csv", 
    skiprows=6, parse_dates=[0], usecols=[0,1], 
    encoding="Shift_JIS", names=["date", "ondo"],
    index_col=0)
print(df_ondo.head())
```
出力は以下の通りです。

```
            ondo
date
2022-01-01   3.4
2022-01-02   3.5
2022-01-03   5.5
2022-01-04   5.2
2022-01-05   4.1
```

それぞれの日の気温が取得できていることが確認できます。

### 電力と気温の散布図
matplotlibのscatter関数を使用して、温度を横軸に、電力使用量を縦軸にして散布図を描画してみます。

```python:ml5.py
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("data/juyo-2022.csv", 
    encoding="shift_jis",
    skiprows=3, parse_dates=[0], names=["date", "kw"], 
    usecols=[0, 2])
df_juyo = df.groupby("date").sum()

df_ondo = pd.read_csv("data/ondo-2022.csv", 
    skiprows=6, parse_dates=[0], usecols=[0,1], 
    encoding="Shift_JIS", names=["date", "ondo"],
    index_col=0)

plt.scatter(df_ondo["ondo"], df_juyo["kw"])         ←(1)
plt.xlabel("temp")                                  ←(2)
plt.ylabel("kw")
plt.show()
```
出力は以下の通りです。

![](capture/ml-graph2.png)

散布図はscatter関数を使用します。(1)がscatter関数を実行している箇所です。X軸の値（温度）と、Y軸の値（電力使用量）を引数に指定します。(2)で、xlabel, ylabel関数でX軸とY軸のラベルを描画しています。

## 機械学習
機械学習では過去のデータからモデルを作成し、未来の値を予測します。今回は、機械学習のモジュールScikit Learnを使って、気温から電力予想をするモデルを作成してみます。

気温と電力消費量のグラフからも、温度と電力消費量に相関がありそうなことが予想できます。放物線（二次関数）のようにみえないでしょうか。今回はモデルを作成するにあたり、単なる直線での近似は難しそうなので（＝放物線のように見えるので）、PolynominalFeaturesというクラスを使用しました。

Scikit Learnでは以下の手順で処理を行います。
1. モデルを作成
2. fitメソッドで学習
3. predictメソッドで予測

さっそく始めてみましょう。

### モデルの作成と予想
以下のサンプルは、今回行ったことのまとめです。モデルを作成、予想、グラフの描画を行っています。

```python:ml6.py
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.preprocessing import PolynomialFeatures        ←(1)
from sklearn.linear_model import LinearRegression

df = pd.read_csv("data/juyo-2022.csv", 
    encoding="shift_jis",
    skiprows=3, parse_dates=[0], names=["date", "kw"], 
    usecols=[0, 2])
df_juyo = df.groupby("date").sum()

df_ondo = pd.read_csv("data/ondo-2022.csv", 
    skiprows=6, parse_dates=[0], usecols=[0,1], 
    encoding="Shift_JIS", names=["date", "ondo"],
    index_col=0)

X = pd.DataFrame(df_ondo["ondo"])
y = df_juyo["kw"]

quadratic = PolynomialFeatures(degree=2)        ←(2)
X_quad = quadratic.fit_transform(X)             ←(3)
model = LinearRegression()                      ←(4)
model.fit(X_quad, y)                            ←(5)

x_test = pd.DataFrame([x for x in range(-5, 40)], columns=["ondo"])     ←(6)
y_quad_fit = model.predict(quadratic.fit_transform(x_test))             ←(7)
plt.plot(pd.DataFrame(x_test), y_quad_fit, color="red")                 ←(8)
plt.scatter(df_ondo["ondo"], df_juyo["kw"])                             ←(9)
plt.show()
```
以下のようにグラフが描画されます。

![](capture/ml-graph3.png)

(1)でScikit Learnで必要なモジュールをインポートします。散布図をみたときに、2次関数で近似できそうだったので、多項式を表現できるPolynomialFeaturesクラスを使用しました。

今回は温度から消費電力を求めます。そのため、温度が説明変数、電力消費量が目的変数となります。データフレームを使ってこれらの値を求める手順は前の例で散布図を描画したときと同じです。

以下が、モデルの作成と学習を行っている箇所です。
```python
quadratic = PolynomialFeatures(degree=2)        ←(2)
X_quad = quadratic.fit_transform(X)             ←(3)
model = LinearRegression()                      ←(4)
model.fit(X_quad, y)                            ←(5)
```
(2)で多項式のオブジェクトを作成し、(3)で説明変数を2次の多項式に変換しています。
(4)でLinearRegressionモデルを作成し、(4)のfitメソッドで学習をします。

fitメソッドで学習するときには、説明変数X_quadと、目的変数yを引数として渡しています。説明変数が問題で、目的変数が答え、つまり、問題と答えを渡して学習しているのです。このように正解を渡して学習する方式を「教師あり学習」と呼びます。人間も学習することで成長するように、機械学習のfitメソッドを実行すると、モデルの学習が行われ、将来の値を予測できるようになります。

scikit-learnでは数多くのモデルが用意されており、それぞれのモデルに対していろいろなパラメタが指定できます。どんなモデルを使用して、どのようなパラメタを設定するか、この辺りは機械学習のノウハウといえるでしょう。

以下がモデルの予想値を表す赤線のデータです。
```python
x_test = pd.DataFrame([x for x in range(-5, 40)], columns=["ondo"])     ←(6)
y_quad_fit = model.predict(quadratic.fit_transform(x_test))             ←(7)
```
(6)で横軸用のデータ（-5から40までの温度）を作成し、変数x_testに代入しています。(7)では、その値をquadratic.fit_transformに引き渡して2次の多項式にして、model.predictメソッドに渡して結果を予想しています。

(8)と(9)では、元データと重ねてプロットしていますが、散布図と重なっていることから、予想モデルがある程度正確であるといえるでしょう。

グラフ描画の部分を以下のように変更すれば、入力した温度での予想電力消費量を出力します。
```python:ml7.py
...
while True:
    t = input("温度を入力してください [改行の入力で終了] ")     ←(1)
    if t == "":
        break
    t = float(t)                                            ←(2)
    x = quadratic.fit_transform([[t]])                      ←(3)
    v = model.predict(x)                                    ←(4)
    print(f"予想使用電力は{v}です")
```
(1)でユーザーからの入力を受け取り、(2)で数値に変換しています。(3)で多項式に変換し、(4)で値を予測する、という手順を繰り返します。modelオブジェクトはすでに学習しているので、入力値を引数で与えると、予測値を出力します。実際に実行すると、温度を入力するとそれらしい値が得られることがわかります。

機械学習は奥の深い分野です。全てをここで説明するのは困難ですが、モデルを作って、学習して、予想する、そんな雰囲気を感じ取っていただければと思います。
