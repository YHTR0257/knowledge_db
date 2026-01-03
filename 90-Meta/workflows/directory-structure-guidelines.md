# Directory Structure Guidelines

## Overview
This document defines the optimal subdirectory organization for the Zettelkasten knowledge management system, based on workflow analysis and content patterns.

---

## 00-Inbox/
**Purpose**: Temporary holding for unprocessed content
**Structure**: Flat (no subdirectories)
**Rationale**: 受信箱はシンプルに保つ。分類前のコンテンツの一時保管のみ

```
00-Inbox/
├── raw-content-1.md
├── raw-content-2.md
└── temp-ideas.md
```

---

## 10-Literature-Notes/
**Purpose**: External source knowledge extraction
**Current Structure**: ✅ Optimized
**Rationale**: メディア種別による分類が情報検索に最適

```
10-Literature-Notes/
├── books/           # 書籍・電子書籍
├── articles/        # 論文・記事・ブログ
├── videos/          # 動画・講義・プレゼン
├── conversations/   # インタビュー・会話・議論
└── podcasts/        # ポッドキャスト・音声コンテンツ
```

---

## 20-Permanent-Notes/
**Purpose**: Atomic knowledge units
**Recommended Structure**: Time-based organization

```
20-Permanent-Notes/
├── 2024/                  # 年別フォルダ
├── 2025/                  # 年別フォルダ
└── 2026/                  # 年別フォルダ
```

**Rationale**:
- タイムスタンプIDによる自然な時系列管理
- 大量化した場合の年フォルダ管理
- 検索主体のアクセスのためフラット配置も許可

---

## 30-Structure-Notes/
**Purpose**: Thematic knowledge organization
**Recommended Structure**: Domain-based hierarchy

```
30-Structure-Notes/
├── domains/                 # 学術・専門分野
│   ├── psychology/
│   ├── technology/
│   ├── philosophy/
│   └── business/
├── methodologies/           # 手法・アプローチ
│   ├── research-methods/
│   ├── thinking-frameworks/
│   └── productivity-systems/
├── interdisciplinary/       # 分野横断テーマ
│   ├── complex-systems/
│   ├── human-computer-interaction/
│   └── sustainability/
└── emerging/               # 新興テーマ・実験的構造
```

**Rationale**:
- 知識の自然な分類に沿った構造化
- 分野横断的研究への対応
- 新しい構造の実験スペース確保

---

## 40-Index-Notes/
**Purpose**: Navigation and discovery support
**Current Structure**: ✅ Well-designed
**Recommended Enhancement**: Add temporal and project-based indexes

```
40-Index-Notes/
├── concepts/               # 概念別索引
├── people/                # 人物別索引
├── methods/               # 手法別索引
├── master-index/          # 全体索引
├── temporal/              # 時系列索引
└── projects/              # プロジェクト関連索引
```

**Enhancement Rationale**:
- 時系列での知識発見支援
- プロジェクト・知識の連携強化

---

## 50-Project-Notes/
**Purpose**: Project lifecycle management with dedicated project directories
**Structure**: Two-state system (active/archived) with project-specific subdirectories

```
50-Project-Notes/
├── 01_active/                    # 進行中のプロジェクト
│   └── YYYYMMDD_project-name/   # プロジェクト専用ディレクトリ
│       ├── PRJ_YYYYMMDD_project-name.md  # メインプロジェクトノート
│       ├── deliverables/        # 成果物（任意）
│       ├── reviews/             # レビュー記録（任意）
│       └── resources/           # プロジェクト固有リソース（任意）
└── 02_archived/                 # 完了・中断したプロジェクト
    └── YYYYMMDD_project-name/   # 同様の構造
```

**Rationale**:
- プロジェクト固有のファイルを一箇所に集約
- active/archived の2状態管理でシンプル化
- プロジェクトディレクトリ内は柔軟な構成を許可

**Naming Conventions**:
- Directory: `YYYYMMDD_project-name/`
- Main note: `PRJ_YYYYMMDD_project-name.md`
- すべて小文字、アンダースコア区切り

---

## 90-Meta/
**Purpose**: System management and configuration
**Structure**: Workflows, templates, and statistics

```
90-Meta/
├── workflows/          # プロセス定義・ガイドライン
├── templates/          # ノートテンプレート
└── statistics/         # システム統計情報
```

---

## Best Practices

### Directory Depth Limitation
**CRITICAL CONSTRAINT**: Maximum 3-level directory depth across all folders
- ✅ Acceptable: `10-Literature-Notes/books/fiction/`
- ❌ Prohibited: `10-Literature-Notes/books/fiction/mystery/detective/`
- ✅ Acceptable: `30-Structure-Notes/domains/psychology/`
- ❌ Prohibited: `30-Structure-Notes/domains/psychology/cognitive/biases/`

**Exception**: Project directories (`50-Project-Notes/01_active/YYYYMMDD_project-name/`) may contain flexible internal structure for project-specific needs.

**Enforcement Strategy**:
- Use filename prefixes for additional categorization
- Leverage tags for multi-dimensional classification
- Use index notes for detailed subcategory management

### Folder Naming
- 全て小文字、ハイフン区切り（プロジェクトディレクトリはアンダースコア）
- 単数形使用（`method/` not `methods/`）
- 略語は避ける（明確性優先）

### Migration Rules
- 既存コンテンツは段階的移行
- バックアップ必須
- リンク整合性チェック

### Maintenance Schedule
- **Weekly**: 新規フォルダ作成の確認
- **Monthly**: 使用していないフォルダの整理
- **Quarterly**: 構造効率性の評価

---

**Last Updated**: 2026-01-03
**Version**: 2.0
**Status**: Updated for new project structure
