---
id: 202512311241-mcp-server-for-documents
created: 2025-12-31
updated: 2025-12-31
tags:
  - permanent
  - "{ domain }":
  - "{ concept_type }":
type: permanent-note
status: draft
confidence: medium
source_type:
  "{ source_type }":
---


# Key Points

- ドキュメント処理に特化したMCPサーバーは4種類存在する
- 用途に応じて選択: Web文書RAG、ローカルファイルRAG、Markdown専用RAG、形式変換ツール

# Context

LLMベースのナレッジ管理において、外部ドキュメントの効率的な検索・変換を実現するMCPサーバーの比較。

## サーバー比較表

| サーバー名                         | 主要機能          | 対象データ    | セットアップ   | プライバシー      | 最適な用途            |
| ----------------------------- | ------------- | -------- | -------- | ----------- | ---------------- |
| **hannesrudolph/mcp-ragdocs** | Web文書RAG      | Web URL  | 簡単（npx）  | 低（OpenAI依存） | 公開ドキュメント検索       |
| **rahulretnan/mcp-ragdocs**   | ローカルRAG + 監視  | ローカルファイル | 複雑（要ビルド） | 高（Ollama可）  | Obsidian Vault管理 |
| **MCP-Markdown-RAG**          | Markdown特化RAG | Markdown | 簡単（uv）   | 最高（完全ローカル）  | プライバシー重視         |
| **markdownify-mcp**           | 形式変換          | 多様な形式    | 中程度      | 高（ローカル処理）   | RAG前処理           |

## 詳細比較

### 1. hannesrudolph/mcp-ragdocs

**できること:**
- Web上の公開ドキュメントをURLから取得してベクトルインデックス化
- 自然言語クエリでセマンティック検索（最大20件）
- URLからのリンク抽出と自動キューイング
- バッチ処理キューの管理

**できないこと:**
- ローカルファイルの直接処理（Web URLのみ）
- リアルタイムファイル監視
- オフライン動作

**pros:**
- セットアップが簡単（npx経由で即実行）
- Qdrantベクターデータベースの強力な検索機能
- 公開ドキュメントに特化した明確な用途

**cons:**
- OpenAI API依存（コスト発生）
- Qdrantサーバーのセットアップが必要
- ローカルファイル未対応

**前提条件:**
- OpenAI APIキー
- Qdrantサーバー（別途起動）
- Node.js環境

**使用するシチュエーション:**
- オンライン技術ドキュメントの一括検索
- 公開されているAPI仕様書の横断検索
- 外部知識ベースの統合検索

**設定例:**

```json
{
  "mcpServers": {
    "rag-docs": {
      "command": "npx",
      "args": ["-y", "@hannesrudolph/mcp-ragdocs"],
      "env": {
        "OPENAI_API_KEY": "sk-...",
        "QDRANT_URL": "http://localhost:6333",
        "QDRANT_API_KEY": "..."
      }
    }
  }
}
```

---

### 2. rahulretnan/mcp-ragdocs

**できること:**
- ローカルリポジトリのインデックス化（Obsidian Vaultなど）
- Ollamaによるローカル埋め込み生成（OpenAIはフォールバック）
- ファイル監視モード（変更検知と自動再インデックス）
- 柔軟なチャンク戦略（semantic, line, character）
- ファイルタイプごとのカスタム設定
- Web UI付属（http://localhost:3030）

**できないこと:**
- Web URLからの直接取得
- 完全なオフライン動作（Qdrantサーバー必要）

**pros:**
- プライバシー優先（Ollama使用でローカル処理可能）
- 充実した機能（リポジトリ管理、ウォッチモード、Web UI）
- ファイルタイプ別の柔軟な設定
- Obsidianユーザーに最適

**cons:**
- セットアップが複雑（要ビルド、npx非対応）
- Qdrantサーバー必須
- ドキュメントがやや分散

**前提条件:**
- Qdrantサーバー（別途起動）
- Ollama（推奨）またはOpenAI APIキー
- Node.js環境
- ビルド環境（npm/yarn）

**使用するシチュエーション:**
- Obsidian Vaultのセマンティック検索
- ローカルドキュメントの変更を自動追跡したい場合
- プライバシーを重視しつつ高機能なRAGが必要
- ファイルタイプごとに異なるチャンク戦略を適用したい場合

**設定例:**

```json
{
  "mcpServers": {
    "rag-docs": {
      "command": "node",
      "args": ["/path/to/mcp-ragdocs/build/index.js"],
      "env": {
        "EMBEDDING_PROVIDER": "ollama",
        "EMBEDDING_MODEL": "nomic-embed-text",
        "FALLBACK_PROVIDER": "openai",
        "QDRANT_URL": "http://localhost:6333"
      }
    }
  }
}
```

**設定例（ワークフローに最適）:**

```json
{
  "repositories": [
    {
      "path": "/Users/yuhi/Obsidian/Research",
      "name": "research-vault",
      "include": ["**/*.md"],
      "exclude": ["**/archive/**", "**/templates/**"],
      "watchMode": true,
      "watchInterval": 5000,
      "chunkSize": 1000,
      "fileTypeConfig": {
        "md": {
          "include": true,
          "chunkStrategy": "semantic",
          "chunkSize": 800
        }
      }
    }
  ]
}
```

---

### 3. Zackriya-Solutions/MCP-Markdown-RAG

**できること:**
- 完全ローカル処理（MilvusファイルベースベクターDB）
- Markdownファイルに特化したセマンティック検索
- 見出しベースの論理的チャンキング（固定）
- 増分インデックス（変更ファイルのみ再処理、`force_reindex=False`）
- 完全再インデックスモード（`force_reindex=True`）
- サブディレクトリの再帰的インデックス（`recursive=True/False`）

**できないこと:**
- Markdown以外のファイル形式（PDF、DOCXなど）
- Web URLからの取得
- リアルタイムファイル監視
- チャンク戦略のカスタマイズ（見出しベース固定）
- チャンクサイズの調整
- ファイルパターンのinclude/exclude設定

**pros:**
- 真のローカルファースト（外部サービス不要）
- プライバシー完全保護（データは全てローカル）
- セットアップが最もシンプル（uv経由）
- Milvusの軽量ファイルベースDB

**cons:**
- 開発初期段階（アクティブ開発中）
- Markdown限定（汎用性低い）
- カスタム設定がほぼできない（force_reindexとrecursiveのみ）
- 初回起動時に埋め込みモデルダウンロード（~50MB）
- 小規模プロジェクト（15スター程度）

**前提条件:**
- Python環境
- uv（Pythonパッケージマネージャ）
- 埋め込みモデル用ディスクスペース（~50MB）

**使用するシチュエーション:**
- 完全にプライバシーを保護したい場合
- Markdownのみを扱うシンプルな用途
- 外部依存を最小限に抑えたい場合
- カスタマイズ不要で即座に使いたい場合

**設定例:**

```json
{
  "mcpServers": {
    "markdown_rag": {
      "command": "uv",
      "args": [
        "--directory",
        "/absolute/path/to/MCP-Markdown-RAG",
        "run",
        "server.py"
      ]
    }
  }
}
```

---

### 4. markdownify-mcp

**できること:**
- 多様な形式をMarkdownへ変換:
  - PDF → Markdown
  - 画像 → Markdown（メタデータ付き）
  - 音声 → Markdown（文字起こし付き）
  - Office文書（DOCX, XLSX, PPTX） → Markdown
  - Webページ → Markdown
  - YouTube動画 → Markdown（トランスクリプト付き）
  - Bing検索結果 → Markdown
- 既存Markdownファイルの取得
- バッチ処理対応

**できないこと:**
- ベクトル検索やRAG機能
- インデックス管理
- セマンティック検索

**pros:**
- 用途が明確（ドキュメント変換に特化）
- 多様な入力形式に対応
- RAGパイプラインの前処理として最適
- 人気プロジェクト（1,800+ スター）

**cons:**
- Windows対応が未完成（PR待ち）
- 単独ではRAG機能なし（他のMCPと組み合わせ必要）
- UTF-8対応が不完全（日本語・中国語で問題あり）

**前提条件:**
- Node.js環境
- uv（Python処理用）
- 変換先Markdownの保存先ディレクトリ

**使用するシチュエーション:**
- 論文PDFをMarkdown化してObsidianに取り込む
- YouTube動画の内容をテキスト化して保存
- Office文書をMarkdownに変換してバージョン管理
- RAGシステムへの入力データ前処理

**補足:**
- 日本語対応が必要な場合は**jdjr2024/markdownify-mcp-utf8**を使用

**設定例:**

```json
{
  "mcpServers": {
    "markdownify": {
      "command": "node",
      "args": ["/path/to/markdownify-mcp/dist/index.js"],
      "env": {
        "UV_PATH": "/path/to/uv",
        "MD_SHARE_DIR": "/path/to/shared/markdown"
      }
    }
  }
}
```

---

## 組み合わせ実用例

### パターン1: 論文研究ワークフロー
1. 論文PDF → Markdown変換（markdownify-mcp）
2. Markdown → Obsidian Vault保存
3. Vaultをインデックス化（rahulretnan/mcp-ragdocs）
4. セマンティック検索で研究支援

### パターン2: プライバシー重視の個人知識ベース
1. ローカルMarkdownファイルを準備
2. MCP-Markdown-RAGでインデックス化
3. 完全ローカル環境でセマンティック検索

### パターン3: 公開技術ドキュメント横断検索
1. オンライン技術ドキュメントのURL収集
2. hannesrudolph/mcp-ragdocsでインデックス化
3. 複数サイトを横断してセマンティック検索

## Related Concepts

- RAG (Retrieval-Augmented Generation) - 基盤技術
  - ベクトル検索によって外部知識をLLMに提供する手法
- Zettelkasten - 応用先
  - 原子的なメモを相互リンクで繋ぐ知識管理手法
- Vector Database - 構成要素
  - Qdrant、Milvusなどのセマンティック検索を実現するDB

## Sources
- GitHub: hannesrudolph/mcp-ragdocs
- GitHub: rahulretnan/mcp-ragdocs
- GitHub: Zackriya-Solutions/MCP-Markdown-RAG
- GitHub: jdjr2024/markdownify-mcp