---
id: 202601051009-comsol-hexamesh
created: 2026-01-05
updated: 2026-01-05
tags:
  - permanent
  - comsol
  - meshing
  - fem
  - geometry-generation
  - voxel
  - hexahedral-mesh
type: permanent-note
status: completed
confidence: high
source_type:
  - technical-implementation
  - software-documentation
---

# Key Points

- **このノートについて**: 3次元グリッドデータから六面体メッシュを用いてFEM解析用ジオメトリを生成する手法
- **対象者**: ボクセルデータ（電子密度、ELF等）からFEMモデルを構築したい研究者・エンジニア
- **関連**: 密度法の代替手法として、明示的なジオメトリ表現を提供
- 境界が均一で周期境界条件(PBC)の適用が容易、バルク弾性特性の計算に最適
- 界面が階段状になるため形状再現性は他手法より劣るが、実装が単純で計算効率が高い

# Context

3次元ボクセルグリッドデータ（電子局在化関数ELF、電荷密度CHGCAR、材料分布等）をFEMで構造解析する際、六面体メッシュによるジオメトリ生成は最も単純かつ直接的な方法です。特に、DFT計算結果を力学シミュレーションに変換する用途や、周期境界条件を用いたバルク特性評価に有効です。密度法（Density Model）と異なり、各要素に明示的に材料特性を割り当てるため、材料の不連続な分布や複合材料の表現が容易です。
# Body

## 六面体メッシュによるジオメトリ生成とは

空間を規則的な六面体（ヘキサヘドロン）要素で区切り、各要素に材料特性を割り当てることで立体構造を表現する手法です。3次元グリッドデータの各ボクセル（体積ピクセル）を1つのFEM要素として扱います。

### 基本概念

- **ボクセル**: 3次元空間の最小単位（2Dピクセルの3D版）
- **六面体要素**: 8つの節点と6つの面を持つ立方体または直方体
- **材料マッピング**: グリッドデータの値（密度、ELF値等）を材料特性に変換

## メリット・デメリット

### ✅ メリット

| 項目 | 詳細 | 適用場面 |
|------|------|----------|
| **🔄 周期境界条件との相性** | 対向面が完全一致、節点ペアリングが容易 | 弾性定数の均質化、結晶構造解析、単位セル解析 |
| **⚡ 実装の単純性** | グリッドデータから直接生成、前処理不要 | 自動化ワークフロー、大量データ処理 |
| **🎯 材料の明示的表現** | 不連続な材料境界を明確に表現可能 | 複合材料、マルチフェーズ構造、DFTデータ変換 |
| **💾 データ構造の直接性** | ボクセルデータをそのまま使用 | ELF/CHGCAR等のグリッドデータ活用 |
| **🔧 自動化の容易さ** | プログラマティックな生成が簡単 | バッチ処理、パラメトリックスタディ |

### ❌ デメリット

| 項目 | 問題点 | 影響 | 対策 |
|------|--------|------|------|
| **📐 形状再現性** | 曲面が階段状（ジャギー）になる | 幾何学的精度低下 | 高解像度化、等値面抽出への移行 |
| **💻 計算コスト** | 要素数がN³で増加 | メモリ・時間の大量消費 | 解像度調整、対称性利用、並列計算 |
| **⚠️ 応力集中** | 階段状境界で数値的応力ピーク発生 | 応力評価の不正確さ | スムージング処理、平均応力評価 |
| **🔍 解像度依存性** | 滑らかな形状には高解像度必須 | 実用性の制限 | 用途を周期構造・バルク特性に限定 |

### 📊 総合評価

**最適な用途**:
- ✅ 周期構造の均質化解析
- ✅ DFTデータ（ELF、CHGCAR）の直接変換
- ✅ バルク弾性定数の計算
- ✅ ボクセルデータの高速FEM化

**不向きな用途**:
- ❌ 滑らかな曲面が重要な構造解析
- ❌ 応力集中の詳細評価
- ❌ トポロジー最適化（密度法の方が適切）
- ❌ 高精度な形状再現が必要な場合

## 密度法との比較

| 項目 | 六面体メッシュ | 密度法（Density Model） |
|------|----------------|-------------------------|
| **実装** | 単純・直接的 | 複雑・最適化が必要 |
| **形状表現** | 階段状（ボクセル的） | 滑らか（フィルタ適用後） |
| **PBC適用** | 容易 | 工夫が必要 |
| **計算効率** | 要素数多い | 要素数少ない（最適化後） |
| **用途** | バルク特性評価 | 形状最適化 |
| **材料割り当て** | 明示的 | 連続的（0〜1） |

**使い分け指針**:
- **六面体メッシュ**: グリッドデータの直接変換、周期構造の解析、バルク特性計算
- **密度法**: トポロジー最適化、滑らかな形状が必要、軽量化設計

## 類似・関連手法

### ジオメトリ生成手法
- **等値面抽出（Isosurface Extraction）** - Marching Cubes法で滑らかな表面を生成
- **Level Set法** - 陰関数で形状を表現、滑らかな境界
- **SIMP法（Solid Isotropic Material with Penalization）** - 密度法の一種、最適化用
- **RAMP法** - SIMP法の改良版、グレースケール抑制
- **Moving Morphable Components (MMC)** - 明示的な幾何形状パラメータを使用

### 選択基準
- **形状の滑らかさ重視** → 等値面抽出、Level Set法
- **最適化が目的** → SIMP法、RAMP法、密度法
- **単純実装・高速** → 六面体メッシュ
- **周期構造解析** → 六面体メッシュ

## COMSOLでの実装プロセス

### 前提となるデータ

**3次元グリッドデータの形式**:
- **座標**: (x, y, z, value)
- **データソース例**:
  - ELF（Electron Localization Function）- VASP, Quantum ESPRESSO
  - 電荷密度（CHGCAR）- VASP
  - 液相・固相マッピング - MD/CFDシミュレーション
  - 材料分布 - 実験データ、イメージング
- **ファイル形式**: txt, csv, hdf5, binary等

**データ要件**:
- グリッド点が等間隔（構造化グリッド）
- 周期境界条件を使う場合、対向面のグリッド点数が一致

### ステップ1: グリッドデータの読み込み

#### 方法A: Interpolation関数を使用（GUI）

```
Definitions > Functions > Interpolation
  - Function name: grid_data
  - Data source: File
  - File: /path/to/griddata.txt
  - Function type: Grid 3D
  - Interpolation method: Linear
```

データファイル形式（例）:
```
% x y z value
0.0 0.0 0.0 0.85
0.1 0.0 0.0 0.82
0.2 0.0 0.0 0.78
...
```

#### 方法B: Java APIによるプログラマティック読み込み

```java
// グリッドデータの読み込み
model.func().create("int1", "Interpolation");
model.func("int1").set("source", "file");
model.func("int1").set("filename", "griddata.txt");
model.func("int1").set("funcs", new String[]{"value", "1"});
model.func("int1").set("struct", "grid");
```

### ステップ2: 六面体メッシュの生成

#### 方法A: Swept Mesh（規則的なグリッド）

```
Mesh > Swept
  - Source: 底面を選択
  - Distribution: Number of elements (Nz)
```

各方向の要素数を指定してグリッド分割。

#### 方法B: Mapped Mesh（より柔軟）

```
Mesh > Mapped
  - Distribution:
    - Edge 1: Number of elements (Nx)
    - Edge 2: Number of elements (Ny)
    - Edge 3: Number of elements (Nz)
```

#### 方法C: Free Tetrahedral → Hexahedral Conversion

四面体メッシュから六面体への変換も可能だが、規則的なグリッドには非推奨。

### ステップ3: 材料特性のマッピング

#### 変数定義による材料特性の割り当て

```
Definitions > Variables
  - Name: E_local
  - Expression: E0 * grid_data(x, y, z)^p
```

```
Definitions > Variables
  - Name: rho_local
  - Expression: rho0 * grid_data(x, y, z)
```

パラメータ:
- `E0`: 基準ヤング率（例: 210e9 [Pa]）
- `rho0`: 基準密度（例: 7850 [kg/m³]）
- `p`: ペナルティパラメータ（通常1〜3、SIMPに倣う場合3）

#### 材料ノードでの適用

```
Materials > Material 1
  - Young's modulus: E_local
  - Poisson's ratio: nu0  (定数または変数)
  - Density: rho_local
```

**注意**: 各要素で材料特性が変化するため、実質的に傾斜機能材料（FGM）として扱われます。

### ステップ4: 周期境界条件の設定（オプション）

弾性定数の均質化解析に必要。

#### Periodic Condition（Solid Mechanics）

```
Solid Mechanics > Periodic Condition
  - Source: 一方の境界面を選択
  - Destination: 対向する境界面を選択
  - Periodicity type: Continuity (変位の連続性)
```

3方向すべてに適用:
- X方向: x=0とx=Lx
- Y方向: y=0とy=Ly
- Z方向: z=0とz=Lz

#### 均質化解析の実装

6つの独立した荷重ケースを実行:
```
Study 1: εxx = 1, 他成分0
Study 2: εyy = 1, 他成分0
Study 3: εzz = 1, 他成分0
Study 4: γyz = 1, 他成分0
Study 5: γzx = 1, 他成分0
Study 6: γxy = 1, 他成分0
```

各ケースで平均応力を計算し、剛性マトリックス C を構成。

### ステップ5: 解析の実行と結果評価

```
Study > Stationary
  - Physics selection: Solid Mechanics
  - Values of dependent variables: User controlled
```

#### 結果の可視化

```
Results > Volume
  - Expression: solid.mises (von Mises応力)
  - Expression: E_local (ヤング率分布)
  - Expression: grid_data(x,y,z) (元データ)
```

#### 弾性定数の抽出

均質化解析の結果から:
```
Results > Derived Values > Average > Volume Average
  - Expression: solid.sx, solid.sy, solid.sz, solid.syz, solid.szx, solid.sxy
```

6つの荷重ケースすべてで実行し、剛性マトリックスを構成。

## 実践例：ELFデータからのメタマテリアル設計

### 背景
DFT計算で得られたELF（Electron Localization Function）データを用いて、原子構造を模倣したメタマテリアル構造をFEMで解析する例。

### データ準備
```python
# VASPのELFデータ（ELFCAR）を読み込み、グリッドデータに変換
import numpy as np
from pymatgen.io.vasp import Elfcar

elf = Elfcar.from_file("ELFCAR")
grid_data = elf.data['total']  # 3D array

# 正規化とスケーリング
grid_normalized = (grid_data - grid_data.min()) / (grid_data.max() - grid_data.min())

# COMSOLで読み込める形式に変換（テキストファイル）
nx, ny, nz = grid_normalized.shape
with open('elf_grid.txt', 'w') as f:
    f.write('% x y z elf_value\n')
    for i in range(nx):
        for j in range(ny):
            for k in range(nz):
                x = i / nx
                y = j / ny
                z = k / nz
                value = grid_normalized[i, j, k]
                f.write(f'{x:.6f} {y:.6f} {z:.6f} {value:.6f}\n')
```

### COMSOLでの設定

1. **ジオメトリ**: 単位セル（例: 1nm × 1nm × 1nm）
2. **Interpolation関数**: `elf_grid.txt`を読み込み
3. **メッシュ**: Mapped mesh、各方向30要素（合計27,000要素）
4. **材料特性**:
   ```
   E_local = E_base * elf_value^3
   rho_local = rho_base * elf_value
   ```
5. **境界条件**: 3方向すべてに周期境界条件
6. **解析**: 6つの均質化荷重ケース
7. **結果**: 有効弾性定数テンソル（21成分）

### 期待される結果
- ELF値が高い領域（結合性が強い）→ 高剛性
- ELF値が低い領域（自由電子的）→ 低剛性
- 異方性弾性定数が原子配置を反映

## Related Concepts

- [[20260104_density-model]] - **代替手法**
    - 六面体メッシュと密度法の比較、用途による使い分け
- [[20260102_comsol-topology-optimization-gui]] - **関連技術**
    - 密度法を用いたトポロジー最適化、SIMP法の実装
- [[20260104_topology-optimization]] - **上位概念**
    - ジオメトリ生成手法の全体像における位置づけ
- 周期境界条件（Periodic Boundary Condition, PBC） - **必須技術**
    - 単位セルからバルク特性を評価する手法
- 均質化法（Homogenization Method） - **解析手法**
    - ミクロ構造から有効弾性定数を計算する理論
- 等値面抽出（Isosurface Extraction） - **形状改善手法**
    - Marching Cubesアルゴリズムで滑らかな表面を生成
- 傾斜機能材料（Functionally Graded Material, FGM） - **類似概念**
    - 材料特性が連続的に変化する材料、実装方法が類似
- ELF（Electron Localization Function） - **データソース**
    - DFT計算で得られる電子の局在性を表す指標

## Sources
- COMSOL Multiphysics Documentation - Mesh User's Guide (Hexahedral Meshing)
- COMSOL Blog - "Meshing Considerations for Linear Static Problems"
- Bendsøe, M. P., & Sigmund, O. (2003). Topology Optimization: Theory, Methods, and Applications
- 均質化法に関する文献: "Homogenization of Composite Materials" by Cherkaev (2000)

<!-- 以下はオプション -->

# 応用例

## 1. 電子構造に基づくメタマテリアル設計

**目的**: DFT計算結果を用いた原子模倣型弾性メタマテリアルの設計

**プロセス**:
1. VASPでELF、電荷密度を計算
2. グリッドデータを抽出・正規化
3. COMSOLで六面体メッシュ生成
4. 周期境界条件下で均質化解析
5. 有効弾性定数を評価

**利点**: 原子レベルの結合情報を直接反映、物理的に妥当な構造

## 2. 複合材料の均質化解析

**目的**: 繊維強化複合材料やセラミック複合材料の有効弾性定数予測

**プロセス**:
1. 材料分布をボクセルデータ化（実験またはシミュレーション）
2. 各ボクセルに材料IDを割り当て
3. 六面体メッシュで離散化
4. 周期境界条件で均質化
5. マクロスケールでの有効特性を計算

**利点**: 複雑な微細構造を直接モデル化、実験データとの照合が容易

## 3. 医用画像からの生体力学解析

**目的**: CT/MRIデータから骨や軟組織の力学特性を評価

**プロセス**:
1. CTデータ（DICOM形式）からボクセルデータ抽出
2. 各ボクセルの輝度値を材料特性にマッピング
3. 六面体メッシュ生成
4. 荷重条件を設定して応力解析
5. 骨密度分布と応力の関係を評価

**利点**: 患者特異的なモデル、実際の形状を忠実に再現

## 4. 多孔質材料の設計

**目的**: 気孔率分布が制御された多孔質材料の力学特性予測

**プロセス**:
1. Phase-fieldや格子ボルツマン法で気孔構造生成
2. ボクセルデータとして出力
3. 固相/気相で材料特性を切り替え（E_solid / E_void ≈ 1e-6）
4. 六面体メッシュで圧縮試験をシミュレーション
5. 有効ヤング率、透過性などを評価

**利点**: 気孔形状の複雑さに対応、製造プロセスとの連携が可能

## トラブルシューティング

### 問題1: メッシュ要素数が多すぎて計算できない

**症状**: メモリ不足、計算時間が長すぎる

**対策**:
- グリッド解像度を下げる（例: 100³ → 50³）
- 対称性を利用してモデルサイズを削減（1/2、1/4、1/8モデル）
- 並列計算を活用（マルチコアCPU、クラスタ計算）
- ソルバーを軽量なものに変更（PARDISO → MUMPS）

### 問題2: 境界が階段状で応力集中が発生

**症状**: 物理的に不自然な応力ピーク

**対策**:
- グリッド解像度を上げる
- 等値面抽出で滑らかな表面を生成してから再メッシュ
- 応力評価時に平均値やスムージングを適用
- 応力集中が重要でない場合は無視

### 問題3: 周期境界条件が正しく適用されない

**症状**: 対向面の変位が一致しない、計算が収束しない

**対策**:
- 対向面のメッシュ節点数が完全に一致しているか確認
- Swept MeshやMapped Meshを使用して構造化メッシュを生成
- 周期境界条件の設定（Source/Destination）が正しいか確認
- ジオメトリのスケールが適切か確認（単位系の統一）

### 問題4: 材料特性のマッピングがうまくいかない

**症状**: 一部の要素で材料特性が異常値になる、NaNが発生

**対策**:
- Interpolation関数の設定を確認（補間方法、外挿の有無）
- グリッドデータの範囲とジオメトリの範囲が一致しているか確認
- 極端に小さい値を避ける（例: E_min = 1e-6 * E0 を下限とする）
- データファイルのフォーマットエラーをチェック

---
- Generated: 2026-01-05
- Purpose: 六面体メッシュを用いたグリッドデータのFEMモデル化手法の包括的ガイド
- Application: DFT-FEM連携、メタマテリアル設計、均質化解析