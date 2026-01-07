---
id: 202601021657-comsol-topology-optimization-gui
created: 2026-01-02
updated: 2026-01-04
tags:
  - permanent
  - comsol
  - gui
  - simulation
  - optimization
  - topology-optimization
  - tutorial
type: permanent-note
status: completed
confidence: high
source_type:
  - software-documentation
  - practical-guide
---

# Key Points

- **このノートについて**: COMSOLのGUIを使用したトポロジー最適化（密度法）の実装ガイド
- **対象者**: GUIで対話的に最適化を実行したい初級〜中級者向け
- **関連**: プログラミング自動化は [[20260104_comsol-topology-optimization-api]] を参照
- Density Model変数を使用して材料分布を定義し、物理インターフェースと連携
- フィルタリング、体積制約、製造制約などをGUIから直感的に設定可能

# Context

COMSOLは多物理シミュレーションソフトウェアで、Optimization Moduleを使用することでトポロジー最適化が実行できます。密度法はその中核となる手法であり、構造、熱、流体、電磁気など様々な物理現象に対して最適化が可能です。このノートでは、COMSOLにおける密度法の具体的な設定方法と実装手順を記録します。

# Body

## COMSOLにおける密度法の実装

### 1. 基本的なワークフロー

```
1. ジオメトリ作成（設計領域の定義）
2. 材料定義
3. 物理インターフェースの設定（Solid Mechanics等）
4. Optimization Moduleの追加
5. Density Model変数の定義
6. 目的関数と制約の設定
7. メッシュ生成
8. 最適化の実行
9. 結果の可視化と後処理
10. レポート生成（オプション）
```

### 2. Topology Optimization モデルの構成

トポロジー最適化を実行するには、Optimization Moduleが必要です。モデルツリーに以下のノードを追加して最適化設定を構築します。

#### Topology Optimization の主要ノード

**Topology Optimization** ノードの下に以下のサブノードを追加できます：

1. **Density Model** ( )
   - 密度分布を定義する主要なノード
   - 設計変数として機能し、材料の配置を最適化
   - 詳細は次のセクションで説明

2. **Fixed Topology Domain** ( )
   - 最適化の対象外とする領域を指定
   - この領域では密度が固定値（通常は1）に保たれる
   - 用途例：取り付け部、接合部など構造的に必須の領域

3. **Fixed Topology Boundary** ( )
   - 境界上で密度を固定する
   - 特定の境界面で材料の存在を強制または禁止
   - 用途例：表面仕上げが必要な境界、接触面など

これらのノードを組み合わせることで、実際の設計制約を反映した現実的な最適化が可能になります。

### 3. Density Model変数の設定

COMSOLでは、密度を表す変数（通常は`theta`または`rho`）を定義します。

#### Definitions > Model Couplings > Density Model
- **変数名**: `theta` （0〜1の連続値）
- **初期値**: 体積率（例: 0.5で50%の材料使用）
- **フィルタタイプ**:
  - `None`: フィルタなし
  - `Helmholtz`: PDE型フィルタ（推奨）
  - `Kernel`: カーネルベースフィルタ
- **フィルタ半径**: メッシュサイズの2〜3倍程度

#### フィルタ半径の設定例
```
フィルタ半径 = 2 * h_mesh
```
ここで`h_mesh`は要素の平均サイズ

### 4. 材料特性の密度依存定義

材料特性（ヤング率など）を密度変数の関数として定義します。

#### SIMP法の実装
```
E_eff = E0 * theta^p
```

COMSOLでの記述例：
- **ヤング率**: `E0 * theta^3`
- **ポアソン比**: `nu` （密度に依存させない）
- **密度（質量密度）**: `rho0 * theta`

パラメータ:
- `E0`: 基材のヤング率（例: 210e9 [Pa]）
- `p`: ペナルティパラメータ（通常3）
- `rho0`: 基材の質量密度（例: 7850 [kg/m³]）

### 5. 最適化設定

#### Optimization ノードの設定

**Control Variables:**
- 変数: `theta`
- 下限: `0` または小さな値（例: `1e-6`）
- 上限: `1`

**Objective Function:**
構造最適化の場合、通常はコンプライアンス（ひずみエネルギー）を最小化：
```
minimize: solid.Ws  % 全ひずみエネルギー
```

**Constraints:**
体積制約（材料使用量の上限）：
```
comp1.intop1(theta) <= V_max
```
ここで`V_max = V_total * volume_fraction`

#### 最適化ソルバー設定
- **Method**: `SNOPT`（推奨）または `MMA`
- **Gradient**: `Adjoint` （感度解析に随伴法を使用）
- **Maximum iterations**: 100〜200
- **Tolerance**: 1e-3

### 6. 製造制約の追加（オプション）

#### 最小部材厚制約
Helmholtzフィルタを使用し、適切なフィルタ半径を設定することで間接的に実現。

#### 対称性制約
ジオメトリ作成時に対称境界条件を使用、または
Optimization設定で対称性制約を明示的に追加。

#### オーバーハング制約（3Dプリント用）
COMSOL 6.0以降では、Build Direction を指定してオーバーハング制約を追加可能。

### 7. メッシュ設定の推奨事項

- **要素タイプ**: 四角形/六面体メッシュ（構造化メッシュ）が望ましい
- **メッシュサイズ**: 設計領域全体で均一に
- **要素数**:
  - 2D問題: 5,000〜50,000要素
  - 3D問題: 50,000〜500,000要素（計算リソース次第）
- **メッシュ品質**: すべての要素で品質 > 0.3を目指す

### 8. 結果の可視化

#### 密度分布の表示
- **Surface plot**: `theta`を可視化
- **Isosurface**: `theta = 0.5`で境界を表示
- **Filter**: `theta > 0.3`で低密度領域を非表示

#### 応力分布の確認
最適化後の形状で応力分布を確認し、応力集中がないか検証：
```
Derived Values > Surface Maximum > solid.mises
```

### 9. レポート生成（Optimization Report）

最適化結果を文書化するために、Optimization Reportノードを使用できます。

#### Optimization Report ノードの使用方法

**Results > Reports > Optimization**

Optimization Reportノードを使用すると、最適化ノード（Density TopologyやFree Shape Domainなど）の設定と選択画像をレポートに含めることができます。

**主要な設定項目**:
- **Source**: レポートに含める最適化ノードを選択
  - 例: `Density Topology 1`, `Free Shape Domain 1`
- **Include selection image**: 選択領域の画像を含める場合にチェック
  - 最適化が適用された領域を視覚的に表示
- **View**: 画像表示用のビューを選択（オプション）
  - `Automatic`: 現在のビューを使用（デフォルト）
  - カスタムビュー: 事前定義されたビューを選択可能
- **Include settings**: デフォルト以外の設定をレポートに含める場合にチェック
  - 目的関数、制約条件、ソルバー設定などが含まれる

**レポート生成の推奨手順**:
1. Results > Reports > Optimization を追加
2. Sourceから対象の最適化ノード（例: Density Topology）を選択
3. "Include selection image" をチェックして選択領域を可視化
4. "Include settings" をチェックして最適化パラメータを記録
5. レポートを生成（HTML, PDF, Word形式に出力可能）

**レポートに含まれる情報**:
- 最適化タイプ（Density Topology, Shape Optimizationなど）
- 設計領域の選択範囲（画像付き）
- 制御変数の設定（上限・下限値）
- 目的関数の定義
- 制約条件（体積制約、製造制約など）
- ソルバー設定（最適化手法、許容誤差、最大反復回数）
- フィルタ設定（タイプ、半径）

この機能により、最適化プロセスの再現性が向上し、結果の共有や文書化が容易になります。

## 実践例：片持ち梁の最適化

### 問題設定
- **設計領域**: 100mm × 40mm の矩形
- **境界条件**: 左端固定、右端下部に荷重 F = 1000N
- **材料**: 鋼（E = 210 GPa, ν = 0.3）
- **体積制約**: 50%以下

### COMSOLでの設定手順

1. **Geometry**: Rectangle 100×40 mm
2. **Materials**: Steel (E=210e9, nu=0.3)
3. **Solid Mechanics**:
   - Domain selection: すべて
   - Young's modulus: `210e9*theta^3`
   - Fixed Constraint: 左端
   - Boundary Load: 右端下部、Force = -1000 N（下向き）
4. **Optimization**:
   - Density Model: theta, Initial value = 0.5
   - Helmholtz filter: radius = 2mm
   - Control variable: theta (0 to 1)
   - Objective: minimize `solid.Ws`
   - Constraint: `comp1.intop1(theta) <= 0.5*100*40`
5. **Mesh**: Physics-controlled mesh, Fine
6. **Study**: Optimization
7. **Compute**

### 期待される結果
- トラス構造的な形状が出現
- 材料が荷重経路に沿って配置される
- 体積が制約値（50%）に収束

## Related Concepts

- [[20260104_comsol-topology-optimization-api]] - **API実装版**
    - Java APIを使用したプログラマティックな実装、自動化・バッチ処理向け
- [[20260104_density-model]] - **理論的基礎**
    - 密度法の一般的な理論と数学的背景
- [[20260104_topology-optimization]] - **上位概念**
    - トポロジー最適化の全体像と様々な手法
- COMSOL Multiphysics - **実装プラットフォーム**
    - マルチフィジックスシミュレーション環境
- Optimization Module - **必須モジュール**
    - COMSOLでトポロジー最適化を実行するための追加モジュール
- 随伴法（Adjoint Method） - **感度解析手法**
    - COMSOLで効率的に勾配を計算する手法
- Helmholtzフィルタ - **数値安定化技術**
    - PDEベースのフィルタリング、チェッカーボード除去に有効

## Sources
- COMSOL Multiphysics Documentation - Optimization Module User's Guide
- COMSOL Application Gallery - Topology Optimization Examples
- COMSOL Blog - "Introduction to Topology Optimization"

<!-- 以下はオプション -->

# トラブルシューティング

## よくある問題と解決策

### 1. 最適化が収束しない
**症状**: 反復回数が最大値に達しても目的関数が減少し続ける

**原因と対策**:
- フィルタ半径が小さすぎる → 2〜3倍に増やす
- メッシュが粗すぎる → メッシュを細かくする
- 初期値が不適切 → 体積率に合わせた初期値（例: 0.5）を設定
- 制約が厳しすぎる → 体積制約を緩和してテスト

### 2. チェッカーボードパターンが出現
**症状**: 密度分布が市松模様になる

**原因と対策**:
- フィルタが適用されていない → Helmholtzフィルタを有効化
- フィルタ半径が小さい → 半径を増やす（メッシュサイズの2倍以上）

### 3. 計算時間が長すぎる
**症状**: 1回の反復に数時間かかる

**原因と対策**:
- メッシュが細かすぎる → 2D問題で2万要素程度に調整
- 3D問題で要素数が多すぎる → 対称性を利用して計算領域を削減
- 随伴法が使われていない → Gradient設定を`Adjoint`に変更

### 4. グレースケールが多い（0/1の明瞭な解が得られない）
**症状**: 中間密度（0.3〜0.7）の領域が広い

**原因と対策**:
- ペナルティパラメータが小さい → p = 3 に設定
- 射影法（Projection）を追加 → Heaviside projectionを有効化
- 継続法を使用 → ペナルティを段階的に増加（p: 1 → 3）

### 5. 物理的に不合理な形状
**症状**: 浮遊した材料、接続されていない領域

**原因と対策**:
- 境界条件が不適切 → 荷重・拘束条件を再確認
- 体積制約が緩すぎる → より厳しい体積制約を設定
- 初期設計が悪い → 均一な初期密度分布を使用

## パフォーマンス最適化のヒント

### 計算効率の向上
1. **対称性の活用**: ジオメトリが対称なら半分または1/4だけモデル化
2. **適切なソルバー選択**: SNOPTは汎用的、MMAは大規模問題で高速
3. **並列計算**: マルチコアCPUを活用（Preferences > Multicore）
4. **メモリ管理**: 不要な出力変数を削除、保存するステップを限定

### 品質向上のコツ
1. **段階的アプローチ**: 粗いメッシュで大まかな形状を把握 → 細かいメッシュで精緻化
2. **複数初期値**: 異なる初期値から最適化を実行し、最良の結果を採用
3. **製造制約の早期導入**: 最初から製造制約を考慮すると後処理が楽
4. **結果の検証**: 最適化後、別途構造解析で応力・変形を確認

## 発展的な応用

### マルチフィジックス最適化
**熱構造連成問題**:
```
Physics:
  - Heat Transfer in Solids
  - Solid Mechanics
Objective:
  - Minimize: 重み付き目的関数 = w1*solid.Ws + w2*ht.maxT
Constraints:
  - Volume constraint
  - Maximum temperature constraint
```

### 複数荷重ケース
```
Study 1: Load Case 1 (荷重パターン1)
Study 2: Load Case 2 (荷重パターン2)
Optimization:
  - Objective: minimize max(solid.Ws@case1, solid.Ws@case2)
```

### 動的荷重下の最適化
```
Physics: Solid Mechanics (Frequency Domain)
Objective: Minimize dynamic compliance at specific frequency
```

---
- Generated: 2026-01-04
- Status: Completed
- Purpose: COMSOL実装ガイド