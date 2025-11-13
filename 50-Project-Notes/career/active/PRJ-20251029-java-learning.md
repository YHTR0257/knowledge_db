
## Project Overview

Javaを学習するためのNotebook

**Goal**: Javaを1人でコンパイルし、多様なソフトとの結合を行う

COMSOLを用いたコンパイルを行い、パラメトリックスタディを行える状態にすることが目標

**Timeline**: 開始時期不明 → 提出締切 2027年2月

**Status**: 進行中

## About Java

Javaとは、大規模プロジェクトで動作することが多いプログラミング言語

Java VM(Virtual Machine)上で動作するため、どのようなプラットフォームの上であっても同様に動作する特徴を持つ

コンテナとの相性が非常に良い

## Learning Path

### Phase 1: 環境構築とビルドツール理解

#### 1.1 開発環境のセットアップ
- JDK 21 (LTS版) のインストール
- VSCodeとExtension Pack for Javaの導入
- Mavenのインストールと設定

詳細: [[20251029-java-setup-vscode]]

#### 1.2 ビルドツールの理解
- Mavenの基礎概念
  - POM (Project Object Model)
  - 依存関係管理
  - ビルドライフサイクル
  - リポジトリシステム

詳細: [[20251029-maven]]

### Phase 2: Java基本文法とデザインパターン

#### 2.1 データ構造とオブジェクト設計
- POJO (Plain Old Java Object) の理解
- JavaBeans規約
- ゲッター/セッターによるカプセル化
- リフレクションの基礎

詳細: [[20251029-java-pojo-and-javabeans]]

#### 2.2 外部ファイル操作
- YAMLファイルの読み込み
- Jacksonライブラリの使用
- 設定ファイルからのデータマッピング
- try-with-resources構文

詳細: [[20251029-java-starter-guide-for-config]]

### Phase 3: COMSOLとの統合 (今後の学習内容)

#### 3.1 COMSOLの基礎理解
- COMSOL APIの構造
- Javaからのメソッド呼び出し
- モデルの読み込みと保存

#### 3.2 パラメトリックスタディの実装
- パラメータの動的設定
- バッチ処理の実装
- 結果の自動収集と保存

#### 3.3 高度な統合
- 並列処理による高速化
- エラーハンドリング
- ログ管理
- 結果の可視化

## Key Concepts Learned

### ビルド管理
- **Maven座標**: groupId, artifactId, versionによるライブラリの一意識別
- **依存関係解決**: セントラルリポジトリからの自動ダウンロード
- **ライフサイクル**: compile → test → package → install の流れ

### プログラミングパターン
- **JavaBeans**: デフォルトコンストラクタ + ゲッター/セッターの規約
- **リフレクション**: 実行時のクラス情報取得とメタプログラミング
- **デシリアライズ**: YAML/JSON → Javaオブジェクトへの自動変換

### 実践的スキル
- VSCodeでのJava開発フロー
- pom.xmlの設定とカスタマイズ
- 外部ライブラリの統合方法
- クラスパスとリソース管理

## Resources and References

### Permanent Notes
- [[20251029-java-setup-vscode]] - VSCodeでのJava開発環境構築
- [[20251029-maven]] - Apache Mavenの基礎
- [[20251029-java-pojo-and-javabeans]] - POJOとJavaBeans
- [[20251029-java-starter-guide-for-config]] - YAML設定ファイル読み込み
- [[20251030-comsol-java-api-setup]] - COMSOLのJava API実行方法について記述

### External Links
- [OpenJDK (Adoptium)](https://adoptium.net/)
- [Apache Maven Documentation](https://maven.apache.org/)
- [Jackson Library](https://github.com/FasterXML/jackson)

## Next Steps

1. **Java基礎文法の復習**
   - 制御構文 (if, for, while)
   - コレクションフレームワーク (List, Map, Set)
   - 例外処理 (try-catch-finally)

2. **オブジェクト指向の深化**
   - 継承とポリモーフィズム
   - インターフェースと抽象クラス
   - ジェネリクス

3. **COMSOL統合の準備**
   - COMSOL API ドキュメントの精読
   - 簡単なモデル操作の実装
   - パラメータスイープの基本実装

4. **実践プロジェクト**
   - YAML設定ファイルからパラメータを読み込み
   - COMSOLモデルに自動適用
   - 結果をCSVに出力するツールの作成

## Progress Log

### 2025-10-29
- 開発環境構築完了 (JDK 21, VSCode, Maven)
- Maven基礎概念の学習
- YAML設定ファイル読み込みサンプルの作成
- POJOとJavaBeansパターンの理解

### Next Session Goals
- Javaの制御構文とコレクションフレームワークの復習
- COMSOL APIドキュメントの調査開始
