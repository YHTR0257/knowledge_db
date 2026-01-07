---
id: ST-software-architecture
created: 2024-09-11
updated: 2026-01-07
tags: [structure, software-engineering, architecture]
type: structure-note
status: active
scope: technical-skills
---

# Software Architecture

## 概要
堅牢で保守性の高いソフトウェアを構築するための設計原則、パターン、および実装技術を体系化した構造ノート。オブジェクト指向設計から、データ構造の選択、そして最新のエコシステムやAPI連携までを網羅する。

## 1. Design Principles & Patterns (設計原則とパターン)
コードの保守性、再利用性、拡張性を高めるための基礎的な設計思想。

### Object-Oriented Design (OOP) & Abstraction
- [[20251111-python-abstract-method]] - 抽象クラスを用いたインターフェース定義と強制
- [[20251010-python-typing-by-protocol]] - PythonにおけるProtocolを用いた構造的サブタイピング (Duck Typing)
- [[20251111-python-class-method]] - クラスメソッドの適切な使用場面とファクトリーパターン
- [[20251111-python-static-method]] - スタティックメソッドの役割とユーティリティ化
- [[20251029-java-pojo-and-javabeans]] - Javaにおけるデータオブジェクトの設計 (POJO/Beans)

## 2. Data Structures in Architecture (アーキテクチャにおけるデータ構造)
システムの要件に応じた適切なデータ表現と構造の選択。

- [[20251029-graph-theory-fundamentals]] - 複雑な関係性をモデル化するグラフ理論
- [[20251029-graph-representation]] - 隣接行列・隣接リストなどの実装パターン
- [[20251029-directed-acyclic-graph]] - 依存関係解決やワークフロー制御におけるDAG (有向非巡回グラフ)
- [[20251029-tree-structure]] - 階層データの表現と走査

## 3. System Integration & APIs (システム統合とAPI)
異なるコンポーネントやシステム間を疎結合に連携させるための技術。

- [[20251231-mcp-server-for-documents]] - LLMと外部データを接続する Model Context Protocol (MCP) アーキテクチャ
- [[20240911-rest-api-architecture]] - RESTful APIの設計原則とベストプラクティス
- **API Design**: インターフェースの統一、ステートレス設計、ドキュメント化

## 4. Modern Development Ecosystem (開発エコシステム)
効率的な開発・ビルド・デプロイを支えるツールチェーンと環境。

### Python Ecosystem
- [[20251203-setup-python-with-uv]] - `uv` を用いた高速なパッケージ管理と環境分離

### Java Ecosystem
- [[20251029-maven]] - 依存関係管理とビルドライフサイクル
- [[20251029-java-starter-guide-for-config]] - プロジェクト構成のベストプラクティス
- [[20251029-java-setup-vscode]] - 開発環境の構築

## 関連構造ノート
- [[ST-data-science-career]] - データサイエンス実装力としてのアーキテクチャ
- [[ST-material-informatics]] - 科学計算ソフトウェアの設計
- [[ST-career-development]] - エンジニアリングキャリア