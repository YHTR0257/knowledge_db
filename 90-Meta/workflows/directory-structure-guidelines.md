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
└── podcasts/        # 🆕 ポッドキャスト・音声コンテンツ
```

**追加提案**:
- `podcasts/` サブフォルダの追加（音声コンテンツの増加に対応）

---

## 20-Permanent-Notes/
**Purpose**: Atomic knowledge units
**Recommended Structure**: Time-based organization with optional thematic grouping

```
20-Permanent-Notes/
├── 2024/                  # 年別フォルダ
├── 2025/                  # 年別フォルダ
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
├── domains/                 # 🆕 学術・専門分野
│   ├── psychology/
│   ├── technology/
│   ├── philosophy/
│   └── business/
├── methodologies/           # 🆕 手法・アプローチ
│   ├── research-methods/
│   ├── thinking-frameworks/
│   └── productivity-systems/
├── interdisciplinary/       # 🆕 分野横断テーマ
│   ├── complex-systems/
│   ├── human-computer-interaction/
│   └── sustainability/
└── emerging/               # 🆕 新興テーマ・実験的構造
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
├── temporal/              # 🆕 時系列索引
└── projects/              # 🆕 プロジェクト関連索引
    ├── active-projects/
    └── completed-projects/
```

**Enhancement Rationale**:
- 時系列での知識発見支援
- プロジェクト・知識の連携強化

---

## 50-Project-Notes/
**Purpose**: Practical project management
**Recommended Structure**: User's specific project categories

```
50-Project-Notes/
├── 01-Career/              # 👩‍💼 進路・就職活動
│   ├── active/
│   ├── complete/
│   └── planning/
├── 02-Artifact/           # 📄 期限付きの成果物（論文、コード、マニュアルなど）
│   ├── active/
│   ├── complete/
│   └── planning/
├── 03-Skill-Building/      # 🧠 スキル・資格の学習（Java, 統計検定など）
│    ├── active/
│    ├── complete/
│    └── planning/
└── meta/                  # プロジェクト管理・テンプレート
    ├── templates/
    ├── methodologies/
    └── reviews/
```

**Rationale**:
- MECE原則に基づくプロジェクト分類
- 個人の目標・活動の状況に合わせて、配置場所を変更する

---

## Best Practices

### Directory Depth Limitation
**CRITICAL CONSTRAINT**: Maximum 3-level directory depth across all folders
- ✅ Acceptable: `10-Literature-Notes/books/fiction/`
- ❌ Prohibited: `10-Literature-Notes/books/fiction/mystery/detective/`
- ✅ Acceptable: `30-Structure-Notes/domains/psychology/`
- ❌ Prohibited: `30-Structure-Notes/domains/psychology/cognitive/biases/`

**Enforcement Strategy**:
- Use filename prefixes for additional categorization
- Leverage tags for multi-dimensional classification
- Use index notes for detailed subcategory management

### Folder Naming
- 全て小文字、ハイフン区切り
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

**Last Updated**: 2025-08-31
**Version**: 1.0
**Status**: Draft for Review