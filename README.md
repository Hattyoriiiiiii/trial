# 機械学習まとめ
「データは21世紀の石油」
データ活用の目指すところ。
なぜデータ活用が必要なのか。
南北戦争での地勢による作戦変更の話がある。会社の日々の売り上げ、受注データ等を毎日頭に入れてそのデータの特徴量を抽出する、機械学習で予測モデルを構築・検証する、これが地勢を徹底的に調べ上げるのと同じでいざというときに力を発揮する。
クラスタリングも地勢を頭に入れる行為と同じ。あくまで手段であり、<span style="color:blue">**クラスタリングによってデータを絞り込み、より深くデータを把握する**</span>。答えは世界をより深く知るところにあり、それに対応するために必要であると。
> [よくわかる最新機械学習の基本と仕組み](https://www.amazon.co.jp/図解入門-よくわかる最新機械学習の基本と仕組み-長橋-賢吾/dp/4798057908)より
###### tags: `Machine Learning` `Python`  `Kaggle`

---
--- 目次 ---
 
[ToC]

---
# 操作技術
## データ操作

```python=
# データセットの読み込み
from sklearn.datasets import load_iris
iris = load_iris()

# データセットの形状を把握する
iris.data.shape

# irisの種類を把握する
iris.target_names
```
## モデル選択

[【機械学習初心者向け】scikit-learn「アルゴリズム・チートシート」の全手法を実装・解説してみた](https://qiita.com/sugulu/items/e3fc39f2e552f2355209)

[![](https://i.imgur.com/vigBrkW.png)](https://scikit-learn.org/stable/tutorial/machine_learning_map/)


## 可視化技術

* pd.read_csv -> df.head() -> sns.pairplot(df)の流れでまずデータの分布をざっと確認

### sns.pairplot
```python=
sns.pairplot(iris, hue='種', kind='scatter', plot_kws={'alpha':0.3})
```
### (sns.violinplot)
```python=
sns.violinplot(x = '種', y = '花弁の長さ',
              data = iris, color='yellow')
```
### sns.swarmplot
```python=
sns.swarmplot(x='種', y='花弁の長さ', data=iris, size=6)
```
### 相関表 -> ヒートマップ
```python=
# データセット'iris'を読み込んで名前を日本語に変える
iris = sns.load_dataset("iris").rename(\
                    columns={"sepal_length": "がくの長さ", "sepal_width": "がくの幅",\
                            "petal_length": "花弁の長さ", "petal_width": "花弁の幅", 
                            "species": "種"})

# 相関表の作成
corr = iris.corr()

# ヒートマップの作成
sns.heatmap(corr, cmap='coolwarm', annot=True)
```
別バージョン
```python=
# データセット'flights'を読み込む
flights = sns.load_dataset('flights')

# データテーブルを作成
flights_pivot = flights.pivot_table(index='month', columns='year', values='passengers')

# ヒートマップを作成
sns.heatmap(flights_pivot)
```

### sns.jointplot
```python=
# xとyをデータセットから切り取ってる
iris_x = iris["花弁の幅"]
iris_y = iris["花弁の長さ"]

# plotする
sns.jointplot(iris_x, iris_y)
```

### smf.ols(最小二乗法)、summary関数
```python=
import statsmodels.formula.api as smf
# 変数にオブジェクトを入れる？
lm_model = smf.ols(formula = "花弁の長さ ~ 花弁の幅", data = iris).fit()
# summary関数を用いて推定結果を表示する
lm_model.summary()
```

---
## 回帰モデル(regression model)
* 相関係数を求める
* 最小二乗法
* 決定係数
* t値とp値を求める
---
## クラスタリング(グループ分類)(clustering)
教師なし学習であり、データ同士の距離(ユークリッド距離、他にデータの相関性に基づいて距離を算出するマハラノビス距離、碁盤上で構成されるのにはマンハッタン距離)を計算することによって距離が近いデータ同士をグループとする。
<span style="color:blue">**クラスタリング(教師なし学習)では「答えが出ない」。データを絞り込み、回帰分析等で予測モデルを作ることになる。こうした技の組み合わせの活用が機械学習において重要なポイントになる**</span>
クラスタリング(clustering)には大きく分けて2つのアプローチがある
1. 似ているデータを集めてクラスタ(cluster)を作る
→ 凝集型クラスタリング(agglomerative clustering)、ボトムアップクラスタリング(bottom-up clustering)という
2. 似ていないデータの境界線を見つけて、データを分割してクラスタを作る
→ 分割型クラスタリング(divisive clustering)、トップダウンクラスタリング(top-down clustering)という

### **階層化クラスタリング**    (hierarchical clustering)
　6つのクラスター併合手法があって、それぞれの手法をもとにデータの特性を見極めるらしい。グループ化したときの階層構造を**樹形図(dendrogram、デンドログラム)という**
 
* <span style="color:blue">**最も近い要素でグループ化する"単連結法"、最も遠い要素をグループ化する"完全連結法"、平均値でグループ分けする"群平均法"、最小の分散でグループ化する"ウォード法"、重心でグループ化する"重心法"、中間点でグループ化する"メディアン法"**</span>
1. **単連結法(single link method)** : 2クラスタ中に含まれてる最も近いデータの距離、外れ値に弱い。細長い鎖状のグループがでやすい方法
2. **完全連結法(complete link method)** : 2クラスタ中に含まれる最も遠いデータ間の距離をグループ間の距離とみなす。外れ値によわい。球状のグループがでやすい。
3. **群平均法(group average method)** : 2つのクラスタに含まれるすべての点同士の距離の平均。外れ値に強い。単連結と完全連結の中間。
4. **ウォード法** : 新しく作るグループ内の分散が最小。外れ値に強い。球状のグループがでやすい。
5. **重心法** : 2つのクラスタの重心同士の距離。直感的にわかりやすい。
6. **メディアン法** : 2つのクラスタの中間点同士の距離。


### **k平均法**    (k-means clustering)
　階層化クラスタリングと違うのは分析者がグループ数を指定する(非階層クラスタリング)。分割型クラスタリング(divisive clustering)で最も有名な手法。
 <span style="color:blue">**「k」は指定するグループの数、「平均法」はグループ分類するそれぞれのデータから中心点までの平均であることから「k平均法」という**</span>
 1. データに対してランダムにグループを割り当てる。
 2. 中心点(セントロイド)を計算する。
 3. データを最も近いセントロイドに再割り当てする。
 4. 繰り返す。
### **k-means++**
k平均法での問題(ランダムにセントロイドを決めるためにセントロイド同士の距離が近いとうまくグループ分類できない)。

### **残差平方和**   (RSS ; residual sum of squares、SSE ; sum of squared errors of prediction) 
　k平均法は分析者によってグループ数が決められる。残差平方和が急激に減少するグループ数を最適なグループ数とみなすことができる。この急激にSSEが下がる点を最適なグループ数とみなす方法を**エルボー法**という。
### **シルエット法**    (Silhouette analysis)
　k平均法では外れ値が発生する可能性がある。この外れ値を把握する方法がシルエット法。シルエット法では、凝縮度と乖離度からシルエット係数(S)を算出することで、**各グループと中心点との距離を可視化する**。
 1. 近さを求める : あるデータと**凝縮度を求める**
* 凝縮度とは : あるデータと、同じグループ内のすべてのデータとの平均距離。近さの指標となる。

 2. 遠さを求める : あるデータと、**乖離度を求める**
* 乖離度とは : あるデータと、最も近くにある別のグループ内の平均距離。別のグループからの遠さの指標となる。

 3. **シルエット係数を求める** : シルエット係数(silhouette coefficient) = 分子に凝縮度から乖離度の差 / 分母に凝縮度か乖離度の大きい方
* シルエット係数が1に近いほど距離が近い指標として判断できる


---
## 主成分分析    (PCA ; principal component analysis)

### 次元削減
データ、変数の数（次元)が多いほど、その分データで表現できる組み合わせが増えるから、結果的に学習の精度が下がる。これを次元の呪いという。
> 相関行列 → 固有値 → カイザー基準 → 因子を特定

* **特徴量抽出** : 文章をそのままモデルに投入しても予測はできない。ある特定のキーワードをカウントをするというように何かしらの特徴を数値に置き換える作業のこと。
* 特徴データをまとめた要素を**特徴ベクトル**といい、この特徴ベクトルが配置されている空間を**特徴空間**という。
* データ分解の最初の一歩が**相関行列**を求めること。
* **固有値**、**固有ベクトル** : 相関行列は固有値と固有ベクトルで構成されていて、相関行列からこれらに分解することでデータを「浮き出す」
* **寄与率** : 3×3行列の場合3個の固有値。合計が３になる固有値に対してどれだけの割合を占めるかを表すのが寄与率
* **カイザー基準** : 固有値が1を割る場合はその因子を考慮する必要がないとされて(**カイザー基準**)、考慮しなくていい因子を削減することによって**次元削減**を実現する
### 因子分析
次元削減のアプローチとして主成分分析がある。これと表裏一体にあるのが因子分析。何かしらの「因子」を発見することが目的。
> 相関行列 → 固有値 → カイザー基準 → 因子を特定
* テストデータを分類するアプローチは、固有値と固有ベクトルへの分解をすること。このデータについて相関行列を求めて固有値を求める。
* 6×6の行列だと固有値は6個。カイザー基準によってどの固有値を考慮すればいいかを判断する。
* **主成分分析では、固有値、固有ベクトル、主成分得点を求めてバイプロットをする流れ**
* **因子分析では、文系理系のような因子を特定した上で、それぞれの因子で説明できる要素(**因子負荷量**)、説明できない要素(**独自因子**)に分解するアプローチ**。
* 主成分分析が総合点を合成するアプローチとすれば、因子分析は要因を分解するアプローチといえる
* **因子軸の回転**
1. **直交回転**は、因子軸が直交することを仮定するモデルです。因子軸が直交するとは、因子間の相関が0であることを意味します。よって、直交回転を行うと因子間相関がすべて0になります
2. **斜交回転**は、因子軸が斜交する、つまり因子間の相関を仮定するモデルです。ただし、データによっては斜交回転を施しても相関がほとんどない場合もあります。

---
## クラス識別
1. **値の入力** : たとえばメールであればメール本文から特徴抽出したデータ。
2. **値の出力** : 値の出力は、+1、-1といった離散値をとる。正常メール、迷惑メールのような2つの値であれば2値(2値分類)、赤、青、緑といった複数のカテゴリの分類であれば複数のカテゴリの分類(多クラス識別)をする。
3. **クラス識別アルゴリズム** : パーセプトロンにおいて、入力データと出力データからの学習にあたって決めるべき要素は**重み**と**バイアス**。
### サポートベクターマシーン    (SVM ; support vector machine)
パーセプトロンに基づいてクラスを明確に分ける境界線を引くための手法(2次元では線で、3次元では平面、n次元では超平面)。クラス識別にあたって余白(マージン)を最大となる境界を決める。
<span style="color:blue">教師データをもとに各入力の優先度をつける重み(β)と発火のためのバイアス(b)を決めるのが**サポートベクターマシン**。(サポートベクターのマージンが最大となる重みβとバイアスbを決定するのがサポートベクターマシンの考え方)
* **パーセプトロン** : カテゴリを出力する考え方で、人間を含めた動物の脳の神経細胞をヒントにしている。この脳の仕組みをコンピューターで実現するのが、クラス識別のパーセプトロンとディープラーニングです。
* **マージン最大化** : 境界(**決定境界**)から最も近いベクトル(特徴量を持った点)との距離を表す。
* **サポートベクトル** : 2つのクラスから最も近い、選ばれた2点ずつのベクトル
* **線形識別と非線形識別について** : クラス識別が全て線形で分離できるとは限らない。線形で分離できない場合は非線形で分離する。非線形で分離する場合は、特徴次元を増やして特徴量を抽出するカーネル法が利用される。データを3次元および多次元空間に変換(これを写像という)することで、特徴抽出をやりやすくする。この手法について、核(カーネル)を抽出する関数をカーネル関数ということから**カーネル法**という。
* **サポートベクターマシンのスパース性** : スパース性は「まばら」という意味。全てのデータが決定境界を決めるサポートベクターとなる必要がないということ。最小二乗法なんかは傾きと切片を求めるために全てのデータを使う。このスパース性によるメリットは計算量が削減されること。
    ### カーネル法 : 次元数を増やすことによって特徴を抽出しやすくなる反面、内積の計算量が増える。
    * **カーネルトリックで計算量を抑える** : できるだけ簡単に内積を計算する方法をカーネルトリックという。**ガウスカーネル**と**多項カーネル**の2つの手法を示す。
     1. ガウスカーネル : ガウスカーネルは、2つのデータについてその差を求めて分散($σ{^2}$)で割ることで特徴量を求める。分散($σ{^2}$)は利用者が決める必要がある。利用者が決めるパラメータを**ハイパーパラメータ**という。
     2. 多項カーネル : 多項カーネルは、写像次元数Tを取る三角関数に似た双曲性正接曲線によって特徴量を抽出するカーネル法。Tの次元によって計算量が変わる。

### 決定木    (decision tree)
パーセプトロン同様にクラスを識別する。「木」とは幹から葉まで重要度に応じて分岐し、この重要度は**ジニ不純度**などによって算出する。
* **ジニ不純度** : まずデータの中の全体に占めるある要素の割合(純度)を求める。そして純度を求めるには、全体に占める要素を採用、つまり、全体に占めるある要素が占める割合が多ければ純度が高い=不純度が低いというわけになる。この不純度の概念は情報の乱雑度をあらわすエントロピーと同じ。


### アンサンブル学習    (Ensemble Learning)
目的 : 複数の学習器を使うことで未学習のデータに対する予測性能を向上させること。教師データを分割して、そのデータを複数のモデルで学習して各モデルの予測を最終的に多数決して決める
* バイアス: 予測値と実現値との誤差の平均のこと
高バイアスだと機械学習モデルは訓練データからしっかりと学習を行えていない可能性が高い
* バリアンス: 予測値がどれだけ散らばっているかを示す度合い
高バリアンスだとモデルは過学習している可能性が高く、新しいデータを使った予測の精度が悪くなる傾向にある
* バイアスとバリアンスはトレードオフの関係にある
#### 3つの手法に分類(バギング、アダブースティング、ランダムフォレスト)
1. **バギング**(Bagging; Bootstrap Aggregating)
* モデルの予測結果のバリアンスを低くする特徴がある
* 予測値と実現値の誤差が大きい場合の改善方法
* バギングを改良した**アダブースティング**がある
    1. **起動フェーズ** : データを教師データとテストデータに分割する。そしてその分割した教師データをランダムに分割して。分割したデータをもとに決定木を構築する(**ブートストラップサンプリング**という)。
    2. **集約フェーズ** : 弱学習器にデータを割り当ててブートストラップサンプリングが終了すると、各学習器は割り当てられたデータから決定木を構築する。そしてテストデータをもとにそれぞれのクラス識別の結果を集計して、多数決で解答を決定する。これが集約のプロセス。

2. **ブースティング**(Boosting)
* モデルの予測精度に対してバイアスを下げる特徴がある
* 間違った予測に「重み」を加味して次のモデルを改善していく

> アダブースティング : ブースティングに何らかの形で学習の仕組みを導入する。バギングとの違いは「重み」があること。**決定木を構築するために線引きして、正しく判別できなかった誤検知データに重みを増やして識別しやすくし、正しく識別できているデータは重みを減らす。この重みの調整の繰り返しによってクラス識別をして最終的にはバギングと同様に多数決でクラス識別をする。アダブースティングはアダプティブ(柔軟に)重みを調整しながらクラス識別をブースティング(後押し)するアプローチ**

3. **スタッキング**(Stacking)
* モデルを積み上げていくことでバイアスとバリアンスをバランスよく調整することができる
* Kaggleなどの機械学習コンペで上位に入ってくるアルゴリズムの多くに、このスタッキングが取り入れられている。


### ランダムフォレスト
バギング、アダブースティングとの異なる点は、決定木の構築方法。決定木を構築する際にランダムに変数を抽出する。これによって、説明変数どうしの依存性を薄め、過学習を抑制する効果がある。以下の手順で決定木を構築する。
1. ブートストラップサンプリング
2. 各弱学習器で決定木を構築する。
* 決定木の構築
1. p個の説明変数からm個の変数をランダムに選択する。選択するmの数は√pであり、100個の説明変数であれば10個選択する。
2. ランダムに選択した変数をもとに、ジニ不純度に基づいて決定木を構築し、それぞれの弱学習器でクラス識別をして、最後にバギングと同様に多数決でクラス識別をする。

#### OOB(Out-of-Bag : 学習に使われなかったデータ)によって重要度の算出する

:star: こうしたランダムフォレスト並びにOOBによる説明変数の重要度は、説明変数が多いケースにおいて有効。


---

## ディープラーニング    (deep learning)

* ニューラルネットワーク    (neural network)

* 誤差関数

* テンソルフロー

* 隠れ層

* 勾配降下法と活性化関数

* 畳み込みニューラルネットワーク
* 転移学習
* 畳み込みオートエンコーダ
* 敵対的生成ネットワーク(GAN)
* 再起型ニューラルネットワーク
* LSTM
* 系列変換モデルと注意機構
* ウェーブネット


---
# Kaggle
t-SNE
xgboost
アンサンブル学習をすることでNNや決定木などのアルゴリズムの弱点を補い合える
t-SNEなどで次元を削減、Stackingで各アルゴリズムの弱点を補う。xgboostなどで識別。重みつき平均で認識結果を組み合わせる。

![](https://i.imgur.com/hbNMTXt.png)



---
# 参考文献
* [よくわかる最新機械学習の基本と仕組み](https://www.amazon.co.jp/図解入門-よくわかる最新機械学習の基本と仕組み-長橋-賢吾/dp/4798057908)
* [[機械学習] iris データセットを用いて scikit-learn の様々な分類アルゴリズムを試してみた](https://qiita.com/ao_log/items/fe9bd42fd249c2a7ee7a)

