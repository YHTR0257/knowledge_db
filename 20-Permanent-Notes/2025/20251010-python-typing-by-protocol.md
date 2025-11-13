---
tags:
  - python
  - typing
  - protocol
  - type-checking
created: 2025-11-06
---

# Python Protocol型による構造的部分型

## 概要

Pythonの`Protocol`は、**構造的部分型（Structural Subtyping）** または **ダックタイピング**を型システムに取り込むための仕組みです。従来の継承ベースの型システムとは異なり、「同じ構造を持っているか」で型の互換性を判定します。

## 基本的な仕組み

### 定義方法

```python
from typing import Protocol
import pandas as pd

class Engine(Protocol):
    def __init__(self, project_id) -> None:
        ...
    
    def save(self, query: str, accepted_byte: float) -> pd.DataFrame:
        ...
```

### 重要なポイント

- `Protocol`を継承したクラスは実装を持たない（インターフェース定義のみ）
- メソッドシグネチャのみを定義し、実装は`...`（Ellipsis）で示す
- このProtocolを明示的に継承しなくても、同じシグネチャを持つクラスは互換性があると見なされる

## 構造的部分型 vs 名目的部分型

### 名目的部分型（Nominal Subtyping）

従来のPythonの継承モデル。明示的な継承関係が必要。

```python
class Animal:
    def speak(self) -> str:
        pass

class Dog(Animal):  # 明示的に継承が必要
    def speak(self) -> str:
        return "Woof"

def make_sound(animal: Animal) -> None:
    print(animal.speak())

dog = Dog()
make_sound(dog)  # OK: Dogは明示的にAnimalを継承している
```

### 構造的部分型（Structural Subtyping）

Protocolを使用。継承関係は不要で、構造が一致すれば互換性がある。

```python
from typing import Protocol

class Speakable(Protocol):
    def speak(self) -> str:
        ...

class Cat:  # Speakableを継承していない
    def speak(self) -> str:
        return "Meow"

def make_sound(animal: Speakable) -> None:
    print(animal.speak())

cat = Cat()
make_sound(cat)  # OK: Catは構造的にSpeakableと互換性がある
```

## 使用する意図

### 1. 柔軟な設計

既存のクラスを変更せずに、新しいインターフェースに適合させることができます。

```python
from typing import Protocol

class Drawable(Protocol):
    def draw(self) -> str: ...

# サードパーティライブラリのクラス（変更不可）
class ThirdPartyShape:
    def draw(self) -> str:
        return "Drawing shape"

# Protocolを使えば、継承なしで互換性を持たせられる
def render(obj: Drawable) -> None:
    print(obj.draw())

shape = ThirdPartyShape()
render(shape)  # OK: 構造が一致している
```

### 2. ダックタイピングの型安全性

Pythonの「アヒルのように鳴けば、それはアヒルだ」という哲学を、静的型チェックと組み合わせます。

```python
from typing import Protocol

class FileLike(Protocol):
    def read(self) -> str: ...
    def write(self, data: str) -> None: ...

def process_file(file: FileLike) -> None:
    content = file.read()
    file.write(content.upper())

# 標準ライブラリのファイルオブジェクト
with open("test.txt", "r+") as f:
    process_file(f)  # OK

# カスタム実装
class StringBuffer:
    def __init__(self):
        self._data = ""
    
    def read(self) -> str:
        return self._data
    
    def write(self, data: str) -> None:
        self._data = data

buffer = StringBuffer()
process_file(buffer)  # OK: 構造が一致
```

### 3. テスト容易性の向上

モックオブジェクトを簡単に作成できます。

```python
from typing import Protocol

class Database(Protocol):
    def query(self, sql: str) -> list: ...
    def execute(self, sql: str) -> None: ...

class MockDatabase:
    def query(self, sql: str) -> list:
        return [{"id": 1, "name": "test"}]
    
    def execute(self, sql: str) -> None:
        print(f"Mock executed: {sql}")

def get_users(db: Database) -> list:
    return db.query("SELECT * FROM users")

# テスト時
mock_db = MockDatabase()
users = get_users(mock_db)  # OK: Protocolに適合
```

## 型チェッカーの動作例

### mypyによる型チェック

#### 成功例

```python
from typing import Protocol

class Engine(Protocol):
    def save(self, query: str, accepted_byte: float) -> str: ...

class PostgreSQLEngine:
    def save(self, query: str, accepted_byte: float) -> str:
        return f"Saved: {query}"

def process(engine: Engine) -> None:
    result = engine.save("SELECT *", 1024.0)
    print(result)

pg_engine = PostgreSQLEngine()
process(pg_engine)  # ✅ OK: 構造が一致
```

**mypy出力**: エラーなし

#### 失敗例1: メソッドシグネチャの不一致

```python
class BrokenEngine:
    def save(self, query: str) -> str:  # accepted_byteが欠けている
        return "Saved"

broken = BrokenEngine()
process(broken)  # ❌ エラー
```

**mypy出力**:
```
error: Argument 1 to "process" has incompatible type "BrokenEngine"; 
expected "Engine"
note: "BrokenEngine" is missing following "Engine" protocol member:
note:     save
```

#### 失敗例2: 戻り値の型の不一致

```python
class WrongReturnEngine:
    def save(self, query: str, accepted_byte: float) -> int:  # 戻り値がint
        return 42

wrong = WrongReturnEngine()
process(wrong)  # ❌ エラー
```

**mypy出力**:
```
error: Argument 1 to "process" has incompatible type "WrongReturnEngine"; 
expected "Engine"
note: Following member(s) of "WrongReturnEngine" have conflicts:
note:     Expected:
note:         def save(self, query: str, accepted_byte: float) -> str
note:     Got:
note:         def save(self, query: str, accepted_byte: float) -> int
```

#### 失敗例3: メソッドの欠如

```python
class IncompleteEngine:
    pass  # saveメソッドがない

incomplete = IncompleteEngine()
process(incomplete)  # ❌ エラー
```

**mypy出力**:
```
error: Argument 1 to "process" has incompatible type "IncompleteEngine"; 
expected "Engine"
note: "IncompleteEngine" is missing following "Engine" protocol member:
note:     save
```

## 実行時の動作

重要な点として、**Protocolは実行時には何もチェックしません**。型チェッカー（mypy、Pyright等）でのみ機能します。

```python
from typing import Protocol

class Engine(Protocol):
    def save(self, query: str) -> None: ...

class BadEngine:
    pass  # saveメソッドがない

def process(engine: Engine) -> None:
    engine.save("test")

# 型チェッカーはエラーを出すが、実行時は...
bad = BadEngine()
process(bad)  # 実行時エラー: AttributeError: 'BadEngine' object has no attribute 'save'
```

### 実行時チェックが必要な場合

`runtime_checkable`デコレータを使用します。

```python
from typing import Protocol, runtime_checkable

@runtime_checkable
class Engine(Protocol):
    def save(self, query: str) -> None: ...

class GoodEngine:
    def save(self, query: str) -> None:
        print("Saved")

class BadEngine:
    pass

good = GoodEngine()
bad = BadEngine()

print(isinstance(good, Engine))  # True
print(isinstance(bad, Engine))   # False
```

## 与える影響

### ポジティブな影響

1. **柔軟性の向上**
   - サードパーティライブラリとの統合が容易
   - 既存コードの変更不要

2. **疎結合の実現**
   - 具体的な実装への依存を減らせる
   - 異なる実装を簡単に差し替えられる

3. **テスタビリティの向上**
   - モックオブジェクトの作成が簡単
   - 依存性の注入が容易

4. **Pythonらしさの維持**
   - ダックタイピングの哲学を保ちつつ型安全性を確保

### ネガティブな影響・注意点

1. **暗黙的な契約**
   - メソッド名とシグネチャが一致していても、意味的に異なる場合がある
   - ドキュメントが重要

2. **実行時チェックの欠如**
   - デフォルトでは実行時に型の検証が行われない
   - `runtime_checkable`を使用すると、若干のオーバーヘッドが発生

3. **複雑性の増加**
   - 継承関係が明示的でないため、コードの追跡が難しくなる場合がある

4. **型チェッカーへの依存**
   - mypyやPyrightなどの型チェッカーを使用しないと効果がない

## ベストプラクティス

### 1. 適切な命名

Protocolの名前は、その役割を明確に示すべきです。

```python
# Good
class Serializable(Protocol): ...
class Closeable(Protocol): ...
class Comparable(Protocol): ...

# Bad
class MyProtocol(Protocol): ...  # 意味が不明確
```

### 2. 最小限のインターフェース

必要最小限のメソッドのみを定義します。

```python
# Good: 必要なメソッドのみ
class Readable(Protocol):
    def read(self) -> str: ...

# Bad: 不要なメソッドまで含める
class FileProtocol(Protocol):
    def read(self) -> str: ...
    def write(self, data: str) -> None: ...
    def seek(self, pos: int) -> None: ...
    def tell(self) -> int: ...
    # ... 他にも多数
```

### 3. ドキュメントの充実

Protocolの意図と期待される動作を明確に記述します。

```python
from typing import Protocol

class Cache(Protocol):
    """
    キャッシュのインターフェース。
    
    実装クラスは以下の動作を保証する必要があります：
    - get: キーが存在しない場合はNoneを返す
    - set: 既存のキーがある場合は上書きする
    - delete: キーが存在しない場合は何もしない
    """
    
    def get(self, key: str) -> str | None:
        """指定されたキーの値を取得する"""
        ...
    
    def set(self, key: str, value: str) -> None:
        """キーと値のペアを保存する"""
        ...
    
    def delete(self, key: str) -> None:
        """指定されたキーを削除する"""
        ...
```

### 4. runtime_checkableの慎重な使用

実行時チェックが本当に必要な場合のみ使用します。

```python
from typing import Protocol, runtime_checkable

@runtime_checkable
class Pluggable(Protocol):
    """プラグインシステムで実行時に型チェックが必要"""
    def initialize(self) -> None: ...
    def execute(self) -> None: ...

def load_plugin(plugin: object) -> None:
    if isinstance(plugin, Pluggable):
        plugin.initialize()
        plugin.execute()
    else:
        raise TypeError("Invalid plugin")
```

## 関連概念

- [[ダックタイピング]]
- [[依存性の注入]]
- [[インターフェース分離の原則]]
- [[型ヒントとアノテーション]]

## 参考リンク

- [PEP 544 – Protocols: Structural subtyping](https://peps.python.org/pep-0544/)
- [typing — Support for type hints](https://docs.python.org/3/library/typing.html#typing.Protocol)
- [Mypy documentation - Protocols and structural subtyping](https://mypy.readthedocs.io/en/stable/protocols.html)

---

**最終更新**: 2025-11-06
