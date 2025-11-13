---
id: 202511110001
created: 2025-11-11
updated: 2025-11-11
tags: [permanent, python, programming, oop, design-pattern]
type: permanent-note
status: complete
confidence: high
source_type: technical-knowledge
---

# Python抽象メソッドによるインターフェース設計

## Core Idea
抽象メソッド（Abstract Method）は、サブクラスで必ず実装すべきメソッドのシグネチャを定義する仕組みであり、インターフェース契約を強制することで型安全性とポリモーフィズムを実現する。

## Context
オブジェクト指向設計において、複数のクラスが同じインターフェースを持つことを保証したい場合がある。抽象メソッドを使うことで、設計時の意図を明示的にコードに反映し、実装漏れをインスタンス化時点で検出できる。

PythonはJavaやC++のような静的型付け言語と異なり、実行時に型チェックを行うため、抽象基底クラス（ABC: Abstract Base Class）という仕組みで明示的にインターフェースを定義する必要がある。

## Key Points
- `abc.ABC`を継承し、`@abstractmethod`デコレータでメソッドを装飾することで定義
- 抽象メソッドを持つクラスは直接インスタンス化できない（TypeErrorが発生）
- サブクラスは全ての抽象メソッドを実装しなければインスタンス化できない
- 抽象メソッドにデフォルト実装を持たせ、`super()`で呼び出すことも可能
- プロパティ（`@property`）と組み合わせて抽象プロパティも定義可能

## Examples

```python
from abc import ABC, abstractmethod

class DataProcessor(ABC):
    """データ処理の抽象基底クラス"""

    @abstractmethod
    def process(self, data: list) -> list:
        """データを処理して返す（サブクラスで実装必須）"""
        pass

    @abstractmethod
    def validate(self, data: list) -> bool:
        """データの妥当性を検証（デフォルト実装あり）"""
        return len(data) > 0  # サブクラスでsuper()経由で利用可能

class CSVProcessor(DataProcessor):
    def process(self, data: list) -> list:
        return [row.split(',') for row in data]

    def validate(self, data: list) -> bool:
        # 親の実装を拡張
        return super().validate(data) and all(',' in row for row in data)

# 実装例
processor = CSVProcessor()  # OK
# base = DataProcessor()    # TypeError: インスタンス化不可
```

抽象プロパティの例：
```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @property
    @abstractmethod
    def area(self) -> float:
        """面積を返す抽象プロパティ"""
        pass

class Circle(Shape):
    def __init__(self, radius: float):
        self._radius = radius

    @property
    def area(self) -> float:
        return 3.14159 * self._radius ** 2
```

## Implications
- **設計の明示化**: インターフェース契約を明示的にコードで表現できる
- **早期エラー検出**: 実装漏れをインスタンス化時点で検出（実行時まで待たない）
- **リファクタリング安全性**: インターフェース変更時に未実装箇所を自動検出
- **ドキュメント効果**: コード自体がインターフェース仕様書として機能
- **チーム開発**: 複数人での開発時に実装すべき内容を明確化

## Counterarguments
- **Pythonicでない**: Pythonの「duck typing」哲学に反するという意見もある
- **オーバーヘッド**: 小規模プロジェクトでは過剰設計になりがち
- **実行時チェック**: 静的型付け言語と異なり、コンパイル時にはチェックされない（`mypy`などの型チェッカーと併用すべき）
- **代替手段**: Protocol型を使えばより柔軟な構造的部分型付けが可能

## Applications
- **プラグインシステム**: 外部拡張可能なアーキテクチャの設計
- **データアクセス層**: Repository、DAO パターンの実装
- **ストラテジーパターン**: アルゴリズムの切り替え可能な設計
- **テンプレートメソッドパターン**: フレームワーク型の処理フロー定義
- **機械学習パイプライン**: 前処理、モデル、評価の統一インターフェース

## Open Questions
- Protocol型との使い分け基準は？（継承が必要か、構造的互換性で十分か）
- 抽象メソッドのデフォルト実装をどこまで提供すべきか？
- 多重継承時の抽象メソッドの扱いは？

## Related Concepts
- [[Python Protocol型による構造的部分型]] - より柔軟なインターフェース定義手法（代替手段）
- [[20251111-python-static-method]] - クラスメソッド制御の別形態（対比）
- [[20251111-python-class-method]] - インスタンス化前のファクトリーメソッド実装（補完）

## Sources
- Python公式ドキュメント: abc — Abstract Base Classes
- PEP 3119 – Introducing Abstract Base Classes
- Effective Python: 継承よりも合成を好む（抽象基底クラスの適切な使用）

---
Generated: 2025-11-11
Model: Claude Sonnet 4.5
