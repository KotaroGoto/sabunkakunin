# 機械学習
Pythonがここまで人気になったのは機械学習関連のモジュールやライブラリの充実があったからといえるでしょう。機械学習に関して掘り下げるとそれだけで書籍何冊分ものボリュームになってしまいます。ここでは「過去の使用電力のデータから、未来の電力利用料を予想する」、そんな作業を通して、機械学習の雰囲気に触れていただければと思います。

## 準備

機械学習にはいろいろなライブラリがありますが、ここではデータ処理用にpandas、機械学習用にscikit learn、グラフの描画にmatplotlibを使用します。いずれも機械学習入門とてもよく利用されるライブラリです。

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
import pandas as pd
df = pd.read_csv("data/juyo-2022.csv", 
    encoding="shift_jis",
    skiprows=3, parse_dates=[0], names=["date", "kw"], 
    usecols=[0, 2])
print(df.head())
```

read_csvには様々な引数が用意されています。今回使用した引数は以下の通りです。
- "data/juyo-2022.csv" ... CSVファイルへのパス
- encoding="shift_jis" ... csvファイルのエンコードがShift_JISになっているのでencoding引数で"shift_jis"を指定
- skiprows=3 ... CSVファイルには最初の３行データ以外の内容が含まれているのでスキップ
- usecols=[0, 2] ... 日と使用量kwの列がほしいので[0,2]と指定
- names=["date", "kw"] ... 列名を指定

実行すると以下のようにCSVの先頭が表示されます。
```
        date    kw
0 2022-01-01  3266
1 2022-01-01  3062
2 2022-01-01  2929
3 2022-01-01  2828
4 2022-01-01  2786
```

元データでは各時間ごとのデータが格納されていました。すなわち1日あたり24行のデータがあったことになります。日毎の合計を求めるためにDataFrameオブジェクトのgroupbyメソッドで日毎にグループわけを行い、sumメソッドでその合計を求めます。

```python:ml2.py
import pandas as pd
df = pd.read_csv("data/juyo-2022.csv", 
    encoding="shift_jis",
    skiprows=3, parse_dates=[0], names=["date", "kw"], 
    usecols=[0, 2])

df_juyo = df.groupby("date").sum()
print(df_juyo.head())
```
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
import matplotlib.pyplot as plt
df = pd.read_csv("data/juyo-2022.csv", 
    encoding="shift_jis",
    skiprows=3, parse_dates=[0], names=["date", "kw"], 
    usecols=[0, 2])

df_juyo = df.groupby("date").sum()
plt.plot(df_juyo)
plt.show()
```

![](capture/ml-graph1.png)

夏と冬に電力消費量が高くなっていることが分かります。ファイルの先頭でmatplotlibのpyplotモジュールをpltという名前でインポートしています。plot関数にDataFrameを渡すとグラフが描画されます。

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

plt.scatter(df_ondo["ondo"], df_juyo["kw"])
plt.xlabel("temp")
plt.ylabel("kw")
plt.show()
```
出力は以下の通りです。

![](capture/ml-graph2.png)

散布図はscatter関数を使用します。X軸の値（温度）と、Y軸の値（電力使用量）を引数に指定します。xlabel, ylabel関数でX軸とY軸のラベルを描画しています。

## 機械学習
気温と電力消費量のグラフからも、温度と電力消費量に相関がありそうなことが予想できます。放物線（二次関数）のようにみえないでしょうか。機械学習のモジュールScikit Learnを使って、気温から電力予想をするモデルを作成してみます。

機械学習では過去のデータからモデルを作成し、未来の値を予測します。今回はモデルを作成するにあたり、単なる直線での近似は難しそうなので、PolynominalFeaturesというクラスを使用しました。モデルを作成したら、fitメソッドで学習し、predictメソッドで予測する、一般的なScikit Learnでの手順となります。

### モデルの作成と予想
以下のサンプルは、今回行ったことのまとめです。モデルを作成、予想、グラフの描画を行っています。

```python:ml6.py
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.preprocessing import PolynomialFeatures
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

quadratic = PolynomialFeatures(degree=2)
X_quad = quadratic.fit_transform(X)
model = LinearRegression()
model.fit(X_quad, y)

x_test = pd.DataFrame([x for x in range(-5, 40)], columns=["ondo"])
y_quad_fit = model.predict(quadratic.fit_transform(x_test))
plt.plot(pd.DataFrame(x_test), y_quad_fit, color="red")
plt.scatter(df_ondo["ondo"], df_juyo["kw"])
plt.show()
```
以下のようにグラフが描画されます。

![](capture/ml-graph3.png)

最初に、必要なモジュールをインポートします。散布図をみたときに、2次関数で近似できそうだったので、多項式を表現できるPolynomialFeaturesクラスを使用しました。

今回は温度から消費電力を求めます。そのため、温度が説明変数、電力消費量が目的変数となります。データフレームを使ってこれらの値を求める手順は前の例で散布図を描画したときと同じです。

以下が、モデルの作成と学習です。
```python
quadratic = PolynomialFeatures(degree=2)
X_quad = quadratic.fit_transform(X)
model = LinearRegression()
model.fit(X_quad, y)
```
説明変数を2次の多項式に変換しているのが最初の2行です。LinearRegressionモデルを作成し、fitメソッドで学習をします。学習の際に説明変数X_quadと、目的変数yを引数として渡しています。これにより、モデルの学習が行われ、将来の値を予測できるようになります。

scikit-learnでは数多くのモデルが用意されており、それぞれのモデルに対していろいろなパラメタが指定できます。どんなモデルを使用して、どのようなパラメタを設定するか、この辺りは機械学習のノウハウといえるでしょう。

以下がモデルの予想値を表す赤線のデータです。
```python
x_test = pd.DataFrame([x for x in range(-5, 40)], columns=["ondo"])
y_quad_fit = model.predict(quadratic.fit_transform(x_test))
```
x_testは-5から40までの温度を表す横軸用のデータです。その値をquadratic.fit_transformに引き渡して2次の多項式にして、model.predictに渡して結果を予想しています。

元データと重ねてプロットしていますが、散布図と重なっていることから、予想モデルがある程度正確であるといえるでしょう。

グラフ描画の部分を以下のように変更すれば、入力した温度での予想電力消費量を出力します。
```python:ml7.py
...
while True:
    t = input("温度を入力してください [改行の入力で終了] ")
    if t == "":
        break
    t = float(t)
    x = quadratic.fit_transform([[t]])
    v = model.predict(x)
    print(f"予想使用電力は{v}です")
```

機械学習は奥の深い分野です。全てをここで説明するのは困難ですが、モデルを作って、学習して、予想する、そんな雰囲気を感じ取っていただければと思います。
