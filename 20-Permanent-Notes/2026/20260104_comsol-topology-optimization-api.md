---
id: 202601041701-comsol-topology-optimization-api
created: 2026-01-04
updated: 2026-01-04
tags:
  - permanent
  - comsol
  - api-reference
  - programming
  - topology-optimization
  - java-api
type: permanent-note
status: completed
confidence: high
source_type:
  - technical-documentation
  - api-reference
---

# Key Points

- **このノートについて**: COMSOL Java APIによるトポロジー最適化（密度法）のプログラミングリファレンス
- **対象者**: 自動化・バッチ処理を実装したい上級者向け
- **関連**: GUI操作は [[20260102_comsol-topology-optimization-gui]] を参照
- DensityTopologyコマンドでプログラマティックにトポロジー最適化モデルを構築可能
- SIMP、RAMP、Darcyなど複数の補間法と、Helmholtzフィルタ、Tanh射影などが利用可能

# Context

COMSOL MultiphysicsのJava APIを使用すると、GUIではなくスクリプトでモデルを構築・実行できます。これにより、パラメトリックスタディの自動化、バッチ処理、カスタムワークフローの構築が可能になります。このノートは、DensityTopologyコマンドの詳細なAPIリファレンスとして、プログラミングによるトポロジー最適化実装の参考資料です。

# Body

## Usage

### 基本的な使用方法

DensityTopologyオブジェクトを作成してトポロジー最適化の設定を定義します。

```java
model.component(<ctag>).common().create(<tag>, "DensityTopology")
```

**パラメータ:**
- `<ctag>`: コンポーネントタグ（通常は "comp1"）
- `<tag>`: DensityTopologyオブジェクトに割り当てる一意のタグ（例: "dt1"）

### 設定例

```java
// DensityTopologyオブジェクトの作成
model.component("comp1").common().create("dt1", "DensityTopology");

// SIMP法の設定
model.component("comp1").common("dt1").set("interpolationType", "SIMP");
model.component("comp1").common("dt1").set("p_SIMP", 3);

// Helmholtzフィルタの設定
model.component("comp1").common("dt1").set("filterType", "Helmholtz");
model.component("comp1").common("dt1").set("L_min", "2*h");  // h はメッシュサイズ

// Tanh射影の有効化
model.component("comp1").common("dt1").set("projectionType", "TanhProjection");
model.component("comp1").common("dt1").set("beta", 8);
model.component("comp1").common("dt1").set("theta_beta", 0.5);

// 初期密度の設定
model.component("comp1").common("dt1").set("theta0", 0.5);
```

## Parameters

| PROPERTY                  | VALUE                                  | DEFAULT                | DESCRIPTION                                                                                                                |
| ------------------------- | -------------------------------------- | ---------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| beta                      | double                                 | 8                      | The projection slope when projectionType is set to TanhProjection.                                                         |
| discretization filterType | linear \| constant                     | Helmholtz \| No_filter | The discretization: linear or constant. The filter type for filtering of the density: Helmholtz filtering or no filtering. |
| interpolationType         | SIMP \| Darcy \| Linear_interp \| RAMP | SIMP                   | The interpolation type: SIMP, Darcy, linear, or RAMP.                                                                      |
| L_min                     | positive double                        | h                      | Filter radius when filterType is set to Helmholtz.                                                                         |
| p_SIMP                    | double                                 | 3                      | SIMP exponent, when interpolationType is set to SIMP.                                                                      |
| projectionType            | No_projection \| TanhProjection        | No_projection          | The projection type: No projection or hyperbolic tangent projection.                                                       |
| q_Darcy                   | double                                 | 0.01                   | Darcy interpolation, when interpolationType is set to Darcy.                                                               |
| q_RAMP                    | double                                 | 3                      | RAMP parameter, when interpolationType is set to RAMP.                                                                     |
| theta0                    | double                                 | 0.5                    | Initial value for the discretization.                                                                                      |
| theta_beta                | double                                 | 0.5                    | The projection point when projectionType is set to TanhProjection.                                                         |
| theta_min                 | double                                 | 0.001                  | Minimum penalized volume fraction, when interpolationType is set to SIMP.                                                  |

## パラメータの詳細解説

### interpolationType（補間タイプ）
材料特性を密度変数の関数として定義する方法を選択します。

#### SIMP（Solid Isotropic Material with Penalization）
**最も一般的な手法**。材料特性を以下のように定義：
```
E(θ) = E₀ · θ^p
```
- `p_SIMP`: ペナルティパラメータ（デフォルト: 3）
- `theta_min`: 最小密度値（デフォルト: 0.001）完全な空洞を避けるため
- **用途**: 構造最適化、熱設計など汎用的

#### RAMP（Rational Approximation of Material Properties）
SIMP法の代替手法。異なる補間関数を使用：
```
E(θ) = E₀ · θ / (1 + q(1-θ))
```
- `q_RAMP`: RAMP補間パラメータ（デフォルト: 3）
- **特徴**: 中間密度に対してSIMPとは異なるペナルティ特性
- **用途**: 特定の物理問題でSIMPより良好な収束を示す場合あり

#### Darcy
**流体最適化専用**。Darcy則に基づく補間：
```
α(θ) = α_max · (1-θ)^q / (θ^3 + q_Darcy)
```
- `q_Darcy`: Darcy補間パラメータ（デフォルト: 0.01）
- **用途**: 流体流路の最適化、多孔質媒体設計

#### Linear_interp（線形補間）
```
E(θ) = E₀ · θ
```
- **特徴**: 最もシンプルだが中間密度が残りやすい
- **用途**: テスト、特殊なケース

### filterType（フィルタタイプ）
チェッカーボードパターンやメッシュ依存性を防ぐためのフィルタリング手法。

#### Helmholtz（推奨）
PDEベースのフィルタリング。以下の偏微分方程式を解く：
```
θ̃ - L_min² · ∇²θ̃ = θ
```
- `L_min`: フィルタ半径（通常はメッシュサイズhの1.5〜3倍）
- **利点**: 明確な数学的定義、メッシュ非依存性
- **計算コスト**: やや高いが、現代的なハードウェアでは問題なし

#### No_filter
フィルタリングなし。
- **使用ケース**: 非常に細かいメッシュ、または他の正則化手法を使用する場合
- **注意**: チェッカーボードパターンが発生しやすい

### projectionType（射影タイプ）
中間密度を抑制し、0/1に近い明瞭な解を得るための手法。

#### TanhProjection（ヘビサイド射影）
双曲線正接関数による射影：
```
θ̄ = (tanh(β·η) + tanh(β(θ̃ - η))) / (tanh(β·η) + tanh(β(1 - η)))
```
- `beta`: 射影の急峻さ（デフォルト: 8）。値が大きいほど急峻
- `theta_beta` (η): 閾値（デフォルト: 0.5）
- **効果**: グレースケール領域を削減、明瞭な境界を生成
- **段階的適用**: betaを徐々に増加（1→4→8→16）すると収束性向上

#### No_projection
射影なし。
- **使用ケース**: フィルタのみで十分な場合、計算コスト削減

### discretization（離散化）
- **linear**: 線形要素（一般的）
- **constant**: 定数要素（特殊なケース）

### theta0（初期密度）
最適化の初期状態における密度分布。

**推奨設定:**
- 体積制約が50%なら `theta0 = 0.5`
- 一様分布からスタートするのが一般的
- **注意**: 初期値により異なる局所解に収束する可能性あり

## 典型的な設定パターン

### パターン1: 標準的な構造最適化
```java
model.component("comp1").common("dt1").set("interpolationType", "SIMP");
model.component("comp1").common("dt1").set("p_SIMP", 3);
model.component("comp1").common("dt1").set("filterType", "Helmholtz");
model.component("comp1").common("dt1").set("L_min", "2*h");
model.component("comp1").common("dt1").set("projectionType", "No_projection");
model.component("comp1").common("dt1").set("theta0", 0.5);
```
**特徴**: シンプルで安定、初期検討に最適

### パターン2: 明瞭な境界が必要な場合
```java
model.component("comp1").common("dt1").set("interpolationType", "SIMP");
model.component("comp1").common("dt1").set("p_SIMP", 3);
model.component("comp1").common("dt1").set("filterType", "Helmholtz");
model.component("comp1").common("dt1").set("L_min", "2*h");
model.component("comp1").common("dt1").set("projectionType", "TanhProjection");
model.component("comp1").common("dt1").set("beta", 8);
model.component("comp1").common("dt1").set("theta_beta", 0.5);
model.component("comp1").common("dt1").set("theta0", 0.5);
```
**特徴**: 製造を意識、CADへのエクスポートが容易

### パターン3: 流体流路の最適化
```java
model.component("comp1").common("dt1").set("interpolationType", "Darcy");
model.component("comp1").common("dt1").set("q_Darcy", 0.01);
model.component("comp1").common("dt1").set("filterType", "Helmholtz");
model.component("comp1").common("dt1").set("L_min", "1.5*h");
model.component("comp1").common("dt1").set("projectionType", "No_projection");
model.component("comp1").common("dt1").set("theta0", 0.5);
```
**特徴**: 流体問題専用の設定

## プログラミング時のベストプラクティス

1. **段階的なbeta増加（継続法）**
```java
// ステップ1: beta = 1
model.component("comp1").common("dt1").set("beta", 1);
model.study("std1").run();

// ステップ2: beta = 4
model.component("comp1").common("dt1").set("beta", 4);
model.study("std1").run();

// ステップ3: beta = 8
model.component("comp1").common("dt1").set("beta", 8);
model.study("std1").run();
```

2. **パラメトリックスタディの自動化**
```java
for (double volfrac = 0.3; volfrac <= 0.7; volfrac += 0.1) {
    model.component("comp1").common("dt1").set("theta0", volfrac);
    model.study("std1").run();
    // 結果を保存
}
```

3. **異なる補間法の比較**
```java
String[] methods = {"SIMP", "RAMP"};
for (String method : methods) {
    model.component("comp1").common("dt1").set("interpolationType", method);
    model.study("std1").run();
    // 結果を比較
}
```

## Related Concepts

- [[20260102_comsol-topology-optimization-gui]] - **GUI実装版**
    - GUIを使用したCOMSOLのトポロジー最適化実装ガイド、このノートはプログラミング版
- [[20260104_density-model]] - **理論的基礎**
    - 密度法の数学的背景と一般理論
- [[20260104_topology-optimization]] - **上位概念**
    - トポロジー最適化の全体像と様々な手法
- COMSOL Java API - **プログラミング環境**
    - モデル構築の自動化、バッチ処理のためのAPI
- Model Builder - **GUI対応物**
    - このコマンドはGUIのDefinitions > Density Modelに対応
- Optimization Module - **必須モジュール**
    - DensityTopologyコマンドを使用するために必要

## Sources
- COMSOL_ProgrammingReferenceManual.pdf
  - Chapter 2: GENERAL COMMANDS
  - Section: DensityTopology
- COMSOL Multiphysics API Reference
- COMSOL Optimization Module User's Guide

<!-- 以下はオプション -->

# 応用例とコード実例

## 完全な最適化モデルの構築例

以下は、片持ち梁のトポロジー最適化をJava APIで完全に構築する例です。

```java
import com.comsol.model.*;
import com.comsol.model.util.*;

public class TopologyOptimizationExample {
    public static void main(String[] args) {
        Model model = ModelUtil.create("Model");

        // ジオメトリの作成
        model.component().create("comp1", true);
        model.component("comp1").geom().create("geom1", 2);
        model.component("comp1").geom("geom1").create("r1", "Rectangle");
        model.component("comp1").geom("geom1").feature("r1").set("size", new String[]{"0.1", "0.04"});
        model.component("comp1").geom("geom1").run();

        // 材料の定義
        model.component("comp1").material().create("mat1", "Common");
        model.component("comp1").material("mat1").propertyGroup("def")
            .set("youngsmodulus", "210e9*dt1.theta^3")  // SIMP法による密度依存
            .set("poissonsratio", "0.3")
            .set("density", "7850*dt1.theta");

        // DensityTopologyの作成
        model.component("comp1").common().create("dt1", "DensityTopology");
        model.component("comp1").common("dt1").selection().all();
        model.component("comp1").common("dt1")
            .set("interpolationType", "SIMP")
            .set("p_SIMP", 3)
            .set("filterType", "Helmholtz")
            .set("L_min", "2*h")
            .set("projectionType", "TanhProjection")
            .set("beta", 8)
            .set("theta_beta", 0.5)
            .set("theta0", 0.5);

        // 構造力学インターフェース
        model.component("comp1").physics().create("solid", "SolidMechanics", "geom1");

        // 境界条件：固定端
        model.component("comp1").physics("solid").create("fix1", "Fixed", 1);
        model.component("comp1").physics("solid").feature("fix1").selection()
            .set(new int[]{1});  // 左端の境界

        // 境界条件：荷重
        model.component("comp1").physics("solid").create("bndl1", "BoundaryLoad", 1);
        model.component("comp1").physics("solid").feature("bndl1").selection()
            .set(new int[]{4});  // 右端下部の境界
        model.component("comp1").physics("solid").feature("bndl1")
            .set("Force", new String[]{"0", "-1000"});

        // メッシュ生成
        model.component("comp1").mesh().create("mesh1");
        model.component("comp1").mesh("mesh1").autoMeshSize(3);  // Fine mesh
        model.component("comp1").mesh("mesh1").run();

        // 最適化Study
        model.study().create("std1");
        model.study("std1").create("opt", "Optimization");

        // 制御変数の設定
        model.study("std1").feature("opt").set("control", "dt1.theta");
        model.study("std1").feature("opt").set("controlmin", 0);
        model.study("std1").feature("opt").set("controlmax", 1);

        // 目的関数：コンプライアンスの最小化
        model.study("std1").feature("opt").set("objective", "comp1.solid.Ws");
        model.study("std1").feature("opt").set("objectivetype", "min");

        // 制約：体積制約
        model.study("std1").feature("opt").set("constraint",
            "comp1.intop1(dt1.theta) <= 0.5*0.1*0.04");

        // ソルバー設定
        model.study("std1").feature("opt").set("optmethod", "SNOPT");
        model.study("std1").feature("opt").set("gradient", "Adjoint");
        model.study("std1").feature("opt").set("maxiter", 150);

        // 計算実行
        model.study("std1").run();

        // 結果の保存
        model.result().export().create("data1", "Data");
        model.result().export("data1").set("filename", "topology_result.txt");
        model.result().export("data1").run();

        System.out.println("Optimization completed!");
    }
}
```

## パラメトリックスタディの例

複数の体積率で最適化を実行し、結果を比較：

```java
public class ParametricTopologyStudy {
    public static void main(String[] args) {
        Model model = ModelUtil.create("ParametricStudy");

        // ... モデル構築（上記と同様）...

        double[] volumeFractions = {0.3, 0.4, 0.5, 0.6};

        for (int i = 0; i < volumeFractions.length; i++) {
            double vf = volumeFractions[i];

            // 初期密度を設定
            model.component("comp1").common("dt1").set("theta0", vf);

            // 体積制約を更新
            String constraint = String.format(
                "comp1.intop1(dt1.theta) <= %f*0.1*0.04", vf);
            model.study("std1").feature("opt").set("constraint", constraint);

            // 計算実行
            model.study("std1").run();

            // 結果を個別に保存
            String filename = String.format("result_vf%d.txt", (int)(vf*100));
            model.result().export("data1").set("filename", filename);
            model.result().export("data1").run();

            System.out.println("Completed for volume fraction: " + vf);
        }
    }
}
```

## GUI操作の記録からスクリプト生成

COMSOLのGUIで操作を行うと、対応するJavaコードが生成されます。

**手順:**
1. COMSOL GUI で Model Builder を使用してモデルを構築
2. File > Model Java Files を選択
3. 生成されたJavaコードを確認
4. DensityTopologyコマンドの部分を抽出・カスタマイズ

## デバッグとトラブルシューティング

### パラメータ値の確認
```java
// 現在の設定を確認
String interpolation = model.component("comp1").common("dt1").getString("interpolationType");
double pSimp = model.component("comp1").common("dt1").getDouble("p_SIMP");
System.out.println("Interpolation: " + interpolation + ", p_SIMP: " + pSimp);
```

### 密度分布の可視化
```java
// 結果から密度分布を取得
double[] theta = model.result().numerical().getData("dt1.theta");
// ヒストグラムなどで分布を確認
```

## API使用の利点

1. **再現性**: スクリプトで完全な再現が可能
2. **自動化**: 大量のケーススタディを自動実行
3. **バージョン管理**: コードをGitで管理可能
4. **カスタムワークフロー**: GUI では実現困難な複雑な処理が可能
5. **ドキュメンテーション**: コメント付きコードが仕様書にもなる

---
- Generated: 2026-01-04
- Status: Completed
- Type: API Reference