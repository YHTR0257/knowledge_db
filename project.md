# Directory Structure
/
├── 00-Inbox/
│   └── processing/       # Notionから移行待ちの内容
│
├── 10-Literature-Notes/
│   ├── books/           # 書籍からの抜粋・要約
│   ├── articles/        # 記事・論文
│   ├── videos/          # 動画・講義
│   └── conversations/   # 会話・議論
│
├── 20-Permanent-Notes/
│   └── [YYYYMMDDHHMM]/  # タイムスタンプID
│
├── 30-Structure-Notes/
│   ├── topics/          # トピック別整理
│   ├── themes/          # テーマ別整理
│   └── arguments/       # 論理構造
│
├── 40-Index-Notes/
│   ├── concepts/        # 概念索引
│   ├── people/          # 人物索引
│   ├── methods/         # 手法索引
│   └── master-index/    # 全体索引
│
├── 50-Project-Notes/
│   ├── active/          # 進行中プロジェクト
│   ├── completed/       # 完了プロジェクト
│   └── templates/       # プロジェクトテンプレート
│
└── 90-Meta/
    ├── workflows/       # 作業フロー
    ├── templates/       # ノートテンプレート
    └── statistics/      # 使用統計・分析

# File naming rule

### 1. Permanent Notes
```
YYYYMMDDHHMM-title-in-english.md
例：202408311534-essence-of-compound-thinking.md
例：202408311620-feedback-loop-principles.md
```

### 2. Literature Notes
```
LN-YYYYMMDD-author-lastname-work-title.md
例：LN-20240831-kahneman-thinking-fast-and-slow.md
例：LN-20240831-openai-gpt4-technical-report.md
例：LN-20240831-brown-attention-is-all-you-need.md
```

### 3. Structure Notes
```
ST-theme-name.md
例：ST-machine-learning-fundamentals.md
例：ST-cognitive-bias-framework.md
例：ST-system-thinking-concepts.md
```

### 4. Index Notes
```
IDX-category-name.md
例：IDX-artificial-intelligence.md
例：IDX-psychology-concepts.md
例：IDX-master-index.md
例：IDX-people.md
例：IDX-methods.md
```

### 5. Project Notes
```
PRJ-YYYYMMDD-project-name.md
例：PRJ-20240831-mcp-knowledge-system.md
例：PRJ-20240901-zettelkasten-setup.md
```

### 補助規則
- **区切り文字**: ハイフン（-）のみ使用
- **大文字**: 使用しない（全て小文字）
- **数字**: 必要に応じて使用可能
- **略語**: 一般的なもの（ai, ml, api等）は使用OK
- **長さ**: 全体で80文字以内を推奨
