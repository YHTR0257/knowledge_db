# Mesh Repair Python Libraries

## Core Idea
メッシュ修復のためのPythonライブラリは、プログラマティックなバッチ処理・自動化パイプライン構築を可能にする。PyMeshLab、PyMeshFix、Trimesh、Open3Dなど、目的と複雑さに応じて選択可能な複数のオプションが存在する。

## Context
3Dデータ処理をCI/CDパイプラインに統合したり、大量のSTLファイルを自動修復する場合、GUIツールではなくスクリプタブルなライブラリが必須である。Pythonエコシステムの充実により、機械学習との統合も容易になっている。

## Main Libraries

### PyMeshLab
- MeshLabのPython APIラッパー
- `meshing_close_holes()`、`meshing_repair_non_manifold_edges()`などの豊富なフィルタ
- インストール: `pip install pymeshlab`
- 用途: MeshLabの機能をスクリプト化したい場合に最適

```python
import pymeshlab
ms = pymeshlab.MeshSet()
ms.load_new_mesh("input.stl")
ms.meshing_repair_non_manifold_edges()
ms.meshing_close_holes(maxholesize=30)
ms.save_current_mesh("repaired.stl")
```

### PyMeshFix
- Marco AtteneのMeshFixソフトウェアのPython/Cythonラッパー
- 高速で堅牢なウォータータイトメッシュ生成に特化
- インストール: `pip install pymeshfix`
- 用途: 3Dプリント前処理の自動化

```python
import pymeshfix
vclean, fclean = pymeshfix.clean_from_arrays(vertices, faces)
```

### Trimesh
- 純Pythonの軽量ライブラリ
- `fill_holes()`メソッドで簡易修復
- インストール: `pip install trimesh`
- 用途: シンプルな修復・可視化・変換処理

```python
import trimesh
mesh = trimesh.load("model.stl")
mesh.fill_holes()
mesh.export("repaired.stl")
```

### Open3D
- TensorベースのモダンなTriangleMeshクラス
- `fill_holes(hole_size=)`で穴サイズ閾値を指定可能
- インストール: `pip install open3d`
- 用途: 点群処理・3Dビジョンとの統合

```python
import open3d as o3d
mesh = o3d.io.read_triangle_mesh("input.stl")
t_mesh = o3d.t.geometry.TriangleMesh.from_legacy(mesh)
filled_mesh = t_mesh.fill_holes(hole_size=1000000.0)
o3d.io.write_triangle_mesh("output.stl", filled_mesh.to_legacy())
```

### MeshLib
- Python・C++対応の最新ライブラリ
- フラット・スムーズ・ナチュラルなど複数の穴埋めモード
- 曲率を考慮した高度な修復機能
- 用途: 高品質な曲面修復が必要な場合

## Selection Criteria
- **修復品質優先**: PyMeshFix、MeshLib
- **機能の豊富さ**: PyMeshLab
- **軽量・シンプル**: Trimesh
- **点群統合**: Open3D

## Implications
- ライブラリの選択は、修復品質・処理速度・既存パイプラインとの統合性のトレードオフである
- 複数ライブラリを組み合わせた修復パイプライン（例: Open3Dで前処理 → PyMeshFixで仕上げ）も有効

## Practical Applications
- CI/CD統合による3Dモデル品質チェック
- バッチ処理による大量STLファイルの自動修復
- 機械学習データセット作成時のメッシュ前処理
- リアルタイム3Dスキャンデータの修復

## Related Concepts
- [[202510081127-mesh-repair-algorithms]] - 背後のアルゴリズム理論
- [[202510081129-mesh-repair-software-tools]] - GUI代替ツール
- [[ST-3d-geometry-processing]] - 3D幾何学処理全般

## Source
- Literature Note: [[LN-20251008-mesh-repair-survey]]

## Metadata
- Created: 2025-10-08
- Tags: #python #mesh-repair #3d-processing #libraries
- Confidence: High
