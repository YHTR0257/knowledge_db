---
id: PRJ-20250831-master-thesis-elastic-metamaterial
created: 2025-08-31
updated: 2025-08-31
tags: [project, active, research, materials-science, metamaterial, dft, machine-learning]
type: project-note
status: active
priority: high
deadline: 2027-02
supervisor: 助教授（未記載）
research_area: materials-engineering
---

# Master Thesis: Elastic Metamaterial Structure Control

## Project Overview

**Goal**: 弾性特性を構造で制御すること - 求める弾性特性から構造を逆推定する

**Core Research Question**: 弾性定数をどのようにすると制御できるのかに注目し、弾性特性と構造の関係性を整理して逆推定システムを構築する

**Timeline**: 開始時期不明 → 提出締切 2027年2月


## Problem Definition

### Problem 1: 弾性特性と構造の関係性整理不足
- 個別の事例研究はあるが、全体像を俯瞰するフレームワークが存在しない
- ミクロな弾性特性とマクロな構造の関係性を整理する必要がある

### Problem 2: 力学メタマテリアル設計指針未確立
- 弾性異方性を再現するための設計指針が確立されていない
- 助教授も含めて、まだ誰も手をつけていない分野であるため設計指針すらも確立されていない

## Core Tasks

### Task 1: 弾性特性と構造の関係性探索
- 弾性定数と電子構造がどのように関連しているのかを探索する
- DFT計算による電子構造解析

### Task 2: 逆推定システム構築
- 弾性特性と構造を関連づけるニューラルネットワークを設計する
- 目標弾性特性から構造パラメータを逆算

## Research Significance

### 学術的貢献
- **新しい設計理論**: 弾性率勾配による波動制御の理論確立
- **最適化手法**: 効率的な逆設計アルゴリズムの開発

### 実用的価値
- **エネルギー効率向上**: 従来比数十倍のエネルギー回収効率実現
- **設計自動化**: 構造自動設計システムの構築
- **産業応用**: IoTセンサー・自立電源システムへの展開

## Technical Approach

### 制御対象パラメータ

**1. 幾何学的パラメータ**
- 単位セル寸法：梁の長さ、円形プレートの半径と厚み
- 密度勾配：弾性率と密度比が振動特性に与える影響
- 高さ分布：スタブ高さと屈折率の相関関係

**2. 材料特性パラメータ**
- 弾性コンプライアンス定数
  - ヤング率の空間分布：E(x,y,z)
  - せん断弾性率：G(x,y,z)
  - ポアソン比：ν(x,y,z)
- 密度分布：ρ(x,y,z)

**研究焦点**: 弾性コンプライアンス定数、密度勾配について注目して制御を行う

## Implementation Strategy

### STEP 1: 弾性特性制御指針の確立

**アプローチ１：電子状態模倣**
- ELF、電子密度、トポロジカルデータを活用
- 梁ー球モデル：結合度を初期参照パラメータとして探索
- 逆梁ー球モデル：メタマテリアル設計への応用
- その他：EDFを直接入力するモデル

**アプローチ２：アモルファス構造模倣**
- 液体からの急冷MDシミュレーション
- 座標データを用いた原子配置
- DFT計算に基づく梁パラメータ設定

### STEP 2: 逆設計システム実装

1. **ニューラルネットワーク構築**
   - GNN（グラフニューラルネットワーク）を用いた逆推定
   - 単純NNを用いた逆推定
   - STLファイル自動生成

2. **検証サイクル**
   - STLファイル → FEM解析 → 実際造形 → 力学試験 → データ蓄積

## Detailed Workflow

### 電子構造計算（DFT）
- **対象系統**: Pure Metal FCC, AB Binary, Amorphous Systems
- **計算パラメータ**: PBE/PBEsol, k-points 15×15×15, ENCUT 520eV
- **解析ツール**: LOBSTER（COHP, COBI, 電荷解析）

### STL構造生成
- **ELF-based**: 電子局在化関数からの構造変換
- **梁-球モデル**: COBI値に基づく梁径・球半径決定
- **品質管理**: 最小特徴サイズ0.05mm、造形性チェック

### COMSOLシミュレーション
- **解析タイプ**: 静的解析、動的解析、特殊試験
- **出力データ**: 弾性定数フルマトリックス、応力分布、派生特性

### 機械学習モデル
- **特徴量**: 電子的特徴、構造的特徴、幾何学的特徴
- **アーキテクチャ**: Multi-branch NN（電子・構造・幾何）
- **出力**: 21要素の剛性マトリックス

## Current Status
- **Stage**: 研究計画段階
- **Progress**: 詳細なワークフロー設計完了
- **Next Actions**: DFT計算の実装開始

## Knowledge Gaps
- DFT計算の具体的実装経験
- COMSOL Java API習熟
- 3D造形技術の詳細知識
- 力学試験プロトコルの実践経験

## Success Metrics
- 弾性特性予測精度: R² > 0.95
- 逆推定誤差: < 5%
- 実験検証精度: 各弾性定数 < 15%誤差

## Resources Required
- **計算資源**: DFT計算用HPC、COMSOL解析環境
- **実験設備**: 3Dプリンター、力学試験機
- **ソフトウェア**: VASP, LOBSTER, COMSOL, PyTorch

## Risk Assessment
- **High Risk**: 電子構造-マクロ構造の関係性が予想より複雑
- **Medium Risk**: 3D造形精度の限界
- **Low Risk**: 機械学習モデルの収束性

---

**Project Timeline**: 2025-08-31 → 2027-02 (修士論文提出)
**Source File**: 00-Inbox/master_thesis.md

[^1]: 2025
