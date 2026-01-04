---
id: 202511210926
created: 2025-11-21
updated: 2025-11-21
tags: [permanent, java, comsol, simulation, design-pattern]
type: permanent-note
status: complete
confidence: high
source_type: technical-knowledge
---

# COMSOL Java API ファイル構造、使用関数学習

このドキュメントでは、COMSOL MultiphysicsのJava APIを使用したシミュレーションモデルの作成と管理に関するファイル構造と主要な使用関数についてまとめます。
一つのファイルをもとにしてCOMSOLで構造計算を行う際に必要な要素をピックアップし、各セクションごとに解説します。

## Core Idea

COMSOL MultiphysicsのJava APIを活用することで、シミュレーションモデルの自動化、カスタマイズ、および効率的な管理が可能となる。主要なファイル構造と関数を理解することで、より効果的にCOMSOLを利用できる。

## Table Of Content

- [定義パラメータについて](##Parameters-定義パラメータについて)
- [Geometry Generation ジオメトリ作成について](##Geometry-generation-ジオメトリ作成について)
- [Mesh Generation メッシュ生成について](##Mesh-generation-メッシュ生成について)
- [Physics Settings 物理場設定について](##Physics-Study-settings-物理場設定について)
- [Solver Settings ソルバー設定について](##solver-settings-ソルバー設定について)
- [Results Visualization and Export 結果の可視化とエクスポートについて](##Results-visualization-and-export-結果の可視化とエクスポートについて)

元データは[こちらのCOMSOLファイル](tmp/comsol_sample_file.java)を参照してください。


## Parameters-定義パラメータについて

| パラメータ名 | データ型 | 説明 |
|--------------|----------|------|
|file|`String`|計算対象のファイル名|
|path|`String`|ファイルのパス|
|stlfile|`String`|STLファイル名|
|lconst|`double`|長さの定数|
|pratio|`double`|ポアソン比|
|dstep|`String`|ステップに関する何か|
|delta_|`double`|ステップ幅|
|aa,bb,cc,bc,ca,ab|`double`|計算系の格子ベクトル。格子ベクトルの大きさとなす角(°)を定義|
|rs, rb|`double`|球の半径、梁の直径|
|e|`double[][]` 二次元配列|基底ベクトル|
|loc_|`double[][]` 二次元配列|球の位置ベクトル|
|d_min, d_max|`double`|梁の長さの最大値と最小値 第一近接と第二近接に対応|
|dpos|`double`|初期位置|
|loc|`double[][]` 二次元配列|球の位置ベクトル|
|nx,ny,nz|`int`|格子定数|
|points|`double[][]` 二次元配列|格子点の位置ベクトル|
|lines|`double[][][]` 三次元配列|格子点を結ぶ線分の端点の位置ベクトル|
|blk_size|`double[3]`||
|uni_size|`double[3]`|Unit cellのサイズ|
|direc|`String`|切り出す結晶面のミラー指数 111 or 100|
|rotate|`double[3][3]`|ミラー指数ごとの格子ベクトルを設定|
|rotate1
|rotate1_

111なら $$ \begin{pmatrix} -1 & 1 & 0 \\-1 & -1 & 2 \\1 & 1 & 1 \\ \end{pmatrix} $$ 100なら $$ \begin{pmatrix} 1 & 0 & 0 \\ 0 & 1 & 0 \\ 0 & 0 & 1 \end{pmatrix} $$


## Model-generation-モデル生成について

一番重要な計算モデル作成。javaファイルの中核部分。

モデルパラメータ

|パラメータ名|単位|説明|設定値|
|------------|----------------|----|----|
|disp||変位の大きさ|0|
|Lx, Ly, Lz|mm|計算領域の大きさ|`uni_size[]`|
|lconst|mm|謎の定数|`lconst`|
|Rs, Rb|mm|球の半径、梁の直径|`rs, rb`|
|E11, E21, E31, E12, E22, E32, E13, E23, E33||ひずみ成分|1|
|delta||ひずみステップ幅|`delta_`|

出力テーブル情報

tbl1~3:ミーゼス応力を出力するテーブル

```java
public class {{class_name}} {
    public static void run(String[] args) {
        //上記のパラメータ定義セクション

        // 125行目
        Model model = ModelUtil.create("Model");

        model.modelPath(path);

        model.label(file + ".mph");
        // パラメータの設定セクション
    }
}
```

## Geometry-generation-ジオメトリ作成について

以下の関数で作成した球と梁のジオメトリに従って、球と梁を配置していきます。

関数一覧
| 関数名 | 引数 | 戻り値 | 説明 |
|--------|------|--------|------|
|`select_bonds`|`double[][]` lines, <br>`double` d_min, <br>`double` d_max|`double[][][]`|与えられた点群でd_minとd_maxの範囲にある線分を選択する関数|
|`add`|`double[][]` list <br> `double[]` a|`double[][]`|二次元配列に一行追加する関数|
|`add_lines`|`double[][]` points|`double[][][]`|点群から全ての線分を生成する関数|
|`unit_tensor`|`double[][]` 
|`transpose`|`double[][]` matrix|`double[][]`|行列の転置を返す関数|

1. `.component("comp1")`でコンポーネントを作成
2. `geom1`でジオメトリを定義
3. `.create(sph,"Sphere")`で球を作成
4. `.feature(sph).set("pos", p)`で球の半径を設定
5. `.feature(sph).set("r", rs)`で球の半径を設定
6. `.create("uni1","Union")`で球を結合
7. `.feature("uni1").selection("input").set(sph)`で球を結合
8. 2個目以降は`.feature("uni1").selection("input").add(sph)`で追加
9. `.create(lll,"Line")`で線分を作成
10. `.feature(lll).set("specify1", "coord")`で線分の端点を指定
11. `.feature(lll).set("specify2", "coord")`で線分の端点を指定
12. `.feature(lll).set("coord1", lines[i][0])`で線分の端点座標を設定
13. `.feature(lll).set("coord2", lines[i][1])`で線分の端点座標を設定
14. `.create(wp,"WorkPlane")`で作業平面を作成
15. `.feature(wp).set("unite", true)`で作業平面を結合
16. `.feature(wp).set("planetype", "normalvector")`で作業平面のタイプを設定
17. `.feature(wp).set("normalvector", ln)`で線分に平行なベクトルを設定
18. `.feature(wp).set("normalcoord", l[0])`で線分の始点を設定
19. `.create("c1","Circle")`で円を作成
20. `.feature("c1").set("r", "Rb")`で球の半径を設定
21. `.create(swp,"Sweep")`でスイープを作成
22. `.feature(swp).set("crossfaces", true)`で断面を閉じる
23. `.feature(swp).set("includefinal", false)`で最終断面を含めない
24. `.feature(swp).selection("face").set(wp+".c1", 1)`で断面を選択
25. `.feature(swp).selection("edge").set(lll+"(1)", 1)`でスイープパスを選択
26. `.feature(swp).selection("diredge").set(lll+"(1)", 1)`でスイープ方向を設定
27. `.feature().move("uni1", i+3+j*3)`でジオメトリを移動
28. `.feature().selection("input").add(swp)`でジオメトリを結合

### Boxの設定

1. `.create("blk0","Block")`でボックスを作成
2. `.feature("blk0").set("pos", new double[]{0.,0.,0.})`でボックスのサイズを設定
3. `.feature("blk0").set("size", uni_size)`でボックスの位置を設定
4. `.create("dif1","Difference")`で差分を作成
5. `.feature().move("dif1", i+2+j*3)`でジオメトリを移動
6. `.feature("dif1").selection("input").set("blk0")`でボックスを選択
7. `.feature("dif1").selection("input2").set("uni1")`でジオメトリを選択
8. 同じ操作でblk1, dif2も作成
9. `.create("boxsel1", "BoxSelection")`でボックス選択を作成
10. `.feature("boxsel1").set("entitydim", 2)`で選択する要素の次元を設定
11. `.feature("boxsel1").label("edgeXn")`でラベルを設定
12. `.feature("boxsel1").set("xmin", "-eps")`でｘ方向の最低値を設定する
13. `.feature("boxsel1").set("xmax", "eps")`でｘ方向の最高値を設定する
14. `.feature("boxsel1").set("condition", "inside")`で選択条件を設定
15. edgeXpを作成し、xminを`Lx - eps`、xmaxを`Lx + eps`に設定。周期境界条件を適用する
16. 同じ操作でedgeYn, edgeYp, edgeZn, edgeZpも作成
17. `geom("geom1").run()`でジオメトリを生成する

### Component-design-コンポーネント設計について

モデルには複数のコンポーネントが含まれます。ジオメトリを作成したモデルに対して、コンポーネントを追加します。

1. `.result().table().create("tbl"+ttt, "Table")`でテーブルを作成
2. tbl1~3:ミーゼス応力を出力するテーブル,tbl4:probe用のテーブル

genext1~3:

1. `.create("genext1","GeometryExtension")`でジオメトリ拡張を作成
2. `.cpl("genext1").selection().named("geom1_boxsel1")`でジオメトリ選択を設定
3. 同じ操作でgenext2, genext3も作成
4. `.cpl("genext1").label("coupleX")`でラベルを設定
5. `.cpl("genext1").set("opname", "coupleX")`で操作名を設定
6. `.cpl("genext1").set("srcframe", "material")`でソースフレームを設定
7. `.cpl("genext1").set("dstmap", new String[]{"X-Lx", "Y", "Z"})`で変換マップを設定
8. coupleY, coupleZも同様に作成。geom1_boxsel2, geom1_boxsel3を選択し、変換マップをそれぞれ設定
9. `.coordSystem("sys1").label("Boundary System 1")`で座標系を作成

## Mesh-generation-メッシュ生成について

1. `.mesh().create("mesh1")`でメッシュを作成
2. `.mesh("mesh1").autoMeshSize(5)`でメッシュサイズを設定。細かさのパラメータとなる
3. `.mesh("mesh1").feature().create("ftri1","FreeTri")`で三角形メッシュを作成
4. ftri1~3:各面に対して三角形メッシュを作成
5. ftri1:geom1_boxsel1を選択
6. ftri2:geom1_boxsel3を選択
7. ftri3:geom1_boxsel5を選択
8. `.create("cpf1","CopyFace")`で面コピーを作成
9. cpf1~3:各面に対して面コピーを作成
10. `.feature("cpf1").selection("source").named("geom1_genext1")`でジオメトリ拡張を選択
11. `.feature("cpf1").selection("destination").named("geom1_boxsel2")`でコピー対象とする面を選択
12. cpf2, cpf3も同様に作成
13. `.mesh("mesh1").create("ftet1", "FreeTet")`で三角形メッシュを作成
14. `.mesh("mesh1").run()`でメッシュを生成

コピー設定

| コピー名 | ソースジオメトリ拡張 | コピー先ジオメトリ選択 |
|----------|----------------------|----------------------|
| cpf1 | geom1_genext1 | geom1_boxsel2 |
| cpf2 | geom1_genext3 | geom1_boxsel4 |
| cpf3 | geom1_genext5 | geom1_boxsel6 |

## Physics-Study-settings-物理場設定について

パラメトリックスタディ＋静的応力解析(Stationary Solid Mechanics)

dispが0からdelta_ずつ増加していく設定

### 材料指定

材料は以下のように設定します。
1. `.material().create("mat1", "Common")`で材料を作成
2. `.propertyGroup("def").set("density", "950")`で密度を設定
3. `.propertyGroup("def").set("poissonsratio", pratio+"-eps")`でポアソン比を設定
4. `.propertyGroup("def").set("youngsmodulus", "10[MPa]")`

### 固体力学

1. `.physics().create("solid", "SolidMechanics", "geom1")`で固体力学を作成
2. `.physics("solid").create("fix1", "Fixed", 0)`で固定境界条件を作成
3. `.feature("fix1").selection().named("geom1_boxsel7")`で固定境界条件を適用する面を選択
4. `.create("disp1", "Displacement2", 2)`で変位境界条件を作成
5. `.feature("disp1").selection().named("geom1_boxsel2")`で変位境界条件を適用する面を選択
6. disp2~3も同様に作成
7. `.feature("disp1").set("Direction", new int[][]{{1}, {1}, {1}})`で変位方向を設定
8. `.feature("disp1").set("U0", new String[][]){以下に示す式})`で変位量を設定
9. `.physics("solid").feature("disp1").label("periodicX")`でラベルを設定
10. `.feature("disp1").featureInfo("info").label("Equation View")`で方程式ビューを設定
11. disp2~3でも同様に設定

変位設定

| 変位名 | 適用面 | 変位設定 |
|--------|--------|----------|
| disp1 | geom1_boxsel2 | X方向にdisp |
| disp2 | geom1_boxsel4 | Y方向にdisp |
| disp3 | geom1_boxsel6 | Z方向にdisp |

式

$$ disp1 = \begin{pmatrix} coupleX(u) - E_{11} * X * disp \\ coupleX(v) - E_{21} * X * disp \\ coupleX(w) - E_{31} * X * disp \end{pmatrix} $$
$$ disp2 = \begin{pmatrix} coupleY(u) - E_{12} * Y * disp \\ coupleY(v) - E_{22} * Y * disp \\ coupleY(w) - E_{32} * Y * disp \end{pmatrix} $$
$$ disp3 = \begin{pmatrix} coupleZ(u) - E_{13} * Z * disp \\ coupleZ(v) - E_{23} * Z * disp \\ coupleZ(w) - E_{33} * Z * disp \end{pmatrix} $$

### プローブ設定

一言で言うと、プローブとは**「シミュレーションの計算中および計算後に、特定の場所や値（スカラー量）の変化をリアルタイムで監視・記録するための『探針（センサー）』」**のことです。


### 1. プローブの基本機能
* **監視対象:** 時間変化、周波数変化、あるいはパラメータ変化に伴う「スカラー量（単一の数値）」を追跡します。実数だけでなく複素数も扱えます。
    * 例：ある点の温度、特定の面にかかる最大応力、流速の平均値など。
* **結果の表示:** 以下の2つの形式でデータを提示します。
    * **表データ (Tabulated data):** 数値のリスト。
    * **1Dグラフ (1D graph plots):** 横軸を時間やパラメータとしたグラフ。

### 2. 計算中と計算後の活用
* **計算中の診断 (While solving):** 計算が正しく進んでいるか、発散していないかを確認するためのモニターとして使えます。
* **計算後の分析 (After computation):** 計算が終わった後に、結果を詳細に分析するために使えます。

### 3. 変数としての活用（重要）
ここが単なるグラフ表示機能と違う高度な点です。
* **変数の定義:** プローブを設定すると、モデル全体（グローバルスコープ）で使える**「変数」**として定義されます。
* **フィードバック:** この変数は、単に結果を見るだけでなく、以下のようなシミュレーションの設定そのものに組み込むことができます。
    * **方程式 (Equations):** 物性値などをプローブの値に応じて変化させる。
    * **境界条件 (Boundary conditions):** プローブの値によって境界条件を変える。
    * **停止条件 (Stop condition):** 「温度が◯◯度を超えたら計算を止める」といった制御に使う。

### 4. 計算中のプロット (Plot while solving)
* 計算をほんの一瞬中断し、プローブのグラフやプロットを描画・更新してから計算を再開する機能です。これにより、ユーザーは計算の進行状況を視覚的にリアルタイムで確認できます。

---

**イメージとしては：**
実験装置の中に「温度計」や「圧力計」を差し込んでおき、実験中にその数値をずっと記録し続けるようなものです。さらに、その温度計の数値を見て「熱くなりすぎたらヒーターを切る（計算を止める）」といった連動操作もできる、という機能になります。

プローブ設定
| プローブ名 | ラベル名 | 監視対象 | 設定値 | 単位 |
|------------|--------|----------|--------|------|
| dom1 | P11 | ミーゼス応力 | `-solid.PxX` | MPa |
| dom2 | P21 | ミーゼス応力 | `-solid.PyX` | MPa |
| dom3 | P31 | ミーゼス応力 | `-solid.PzX` | MPa |
| dom4 | P12 | ミーゼス応力 | `-solid.PxY` | MPa |
| dom5 | P22 | ミーゼス応力 | `-solid.PyY` | MPa |
| dom6 | P32 | ミーゼス応力 | `-solid.PzY` | MPa |
| dom7 | P13 | ミーゼス応力 | `-solid.PxZ` | MPa |
| dom8 | P23 | ミーゼス応力 | `-solid.PyZ` | MPa |
| dom9 | P33 | ミーゼス応力 | `-solid.PzZ` | MPa |

### スタディ設定

1. `.study().create("std1")`でスタディを作成
2. `.setStoreSolution(true)`で解を保存
3. `.create("param", "Parametric")`でパラメトリックスタディを作成
4. `.create("stat", "Stationary")`で静的解析を作成

### ソルバー設定

1. `.sol().create("sol1")`でソルバーを作成
2. `.study("std1")`でスタディを選択
3. `.create("st1", "StudyStep")`でスタディステップを作成
4. `.create("v1", "Variables")`で変数を作成
5. `.create("s1", "Stationary")`で静的ソルバーを作成
6. `.feature("s1").create("p1", "Parametric")`でパラメトリックソルバーを作成
7. `.feature("s1").create("fc1", "FullyCoupled")`で完全結合ソルバーを作成
8. `.feature("s1").create("se1", "Segregated")`で分離ソルバーを作成
9. `.feature("s1).feature().remove("fcDef")`でデフォルトの完全結合ソルバーを削除


### ジョブ構成
1. `.batch().create("p1", "Parametric")`でバッチジョブを作成
2. `.batch("p1").study("std1")`でスタディを選択

## 結果出力

1. `.result().dataset("dset1").set("probetag", "dom9")`でデータセットを作成
### ひずみの与え方

ひずみはe11~e33まで9成分を与えることで設定
初期値を1に設定する。

## solver-settings-ソルバー設定について

## Results-visualization-and-export-結果の可視化とエクスポートについて

## Examples
{{concrete_example}}

## Implications
- {{implication1}}
- {{implication2}}

## Counterarguments
{{potential_objections}}

## Applications
{{practical_uses}}

## Open Questions
- {{question1}}
- {{question2}}

## Related Concepts
- {{related1}} - {{relationship_type}}
- {{related2}} - {{relationship_type}}

## Sources
- {{source1}}
- {{source2}}

---
Generated: {{generation_timestamp}}
Model: {{ai_model_used}}