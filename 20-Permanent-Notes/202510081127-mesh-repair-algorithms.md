# Mesh Repair Algorithms

## Core Idea
メッシュ修復アルゴリズムは、3Dモデルの欠損・穴・自己交差などの幾何学的欠陥を自動的に検出・修正する技術である。穴のサイズ・形状・周囲の曲率に応じて最適な修復手法を選択することで、高品質な3Dメッシュを生成する。

## Context
3Dスキャン、CADモデリング、リバースエンジニアリングの過程で生成されるメッシュは、しばしば欠損や位相的エラーを含む。特に3Dプリント用途では「ウォータータイト（watertight）」なメッシュが必須であり、修復アルゴリズムの重要性が高い。

## Main Algorithms

### Advancing Front Mesh (AFM) Method
- 穴の境界エッジから内側に向けて新しい三角形を段階的に生成
- ポアソン方程式を使用して頂点位置を最適化
- 中〜大サイズの穴の修復に適している
- 周囲のメッシュ密度との整合性が高い

### Size-Based Classification Approach
穴のサイズに応じて異なる戦略を適用：
- **小さい穴**: 重心ベース法（境界頂点の重心を利用した単純な三角形分割）
- **中サイズの穴**: 最小面積三角形分割（視覚的な自然さを優先）
- **大きい穴**: 改良AFM法（曲率情報を活用した高度な補完）

### Curvature-Adaptive Repair
- 周囲のメッシュ曲率を解析して補完形状を決定
- ガウス曲率・平均曲率を考慮した自然な形状生成
- 有機形状・自由曲面の修復に優れる
- Materialise Magicsなどのプロフェッショナルツールで実装

## Implications
- アルゴリズムの選択は、修復対象の用途（3Dプリント vs ビジュアライゼーション）に依存する
- 完全自動修復は難しく、ユーザーの意図を反映する半自動アプローチが実用的な場合も多い
- 処理速度と品質のトレードオフが存在し、リアルタイム処理には簡易アルゴリズムが選ばれる

## Counterarguments / Limitations
- 大規模な欠損や形状情報が完全に失われた領域では、アルゴリズム単独での修復は困難
- 元の設計意図と異なる補完が行われるリスクがある
- 複雑な位相構造（トーラス形状など）の修復は依然として研究課題

## Practical Applications
- 3Dプリント前処理（STL修復）
- 文化財デジタルアーカイブ（スキャンデータの欠損補完）
- 医療画像処理（CT/MRIデータからの3Dモデル生成）
- ゲーム・映画のCG制作

## Related Concepts
- [[202510081128-mesh-repair-python-libraries]] - Python実装ライブラリ
- [[202510081129-mesh-repair-software-tools]] - GUIツール比較
- [[ST-3d-geometry-processing]] - 3D幾何学処理全般

## Source
- Literature Note: [[LN-20251008-mesh-repair-survey]]

## Metadata
- Created: 2025-10-08
- Tags: #mesh-repair #algorithms #3d-processing #geometry
- Confidence: High
