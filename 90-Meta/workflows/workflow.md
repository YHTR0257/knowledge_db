## Zettelkasten × MCP ワークフロー

### 1. 情報収集・初期処理フロー

```
Notion (Fleeting Notes)
    ↓
Daily Review Process
    ↓
Obsidian Inbox (00-Inbox/processing/)
    ↓
AI Classification (Claude Code/Gemini CLI)
    ├─ Literature Note → 20-Literature-Notes/
    ├─ Quick Concept → Direct to Permanent
    └─ Project Item → 50-Project-Notes/
```

**AI指示例**:
```
Classify this content from Notion:
"Machine learning bias in hiring algorithms - Harvard Business Review article about algorithmic fairness"

Output: Literature note template with domain=AI, subdomain=ethics
```

### 2. Literature → Permanent 変換フロー

```
Literature Note (完成)
    ↓
AI Analysis
    ├─ Extract 2-3 core concepts
    ├─ Generate permanent note templates
    └─ Suggest connections to existing notes
    ↓
Human Review & Refinement
    ↓
Permanent Notes (20-Permanent-Notes/)
```

**AI指示例**:
```
From literature note LN-20240831-kahneman-thinking-fast-and-slow:
- Extract 3 permanent notes
- Focus on System 1/System 2 distinction, availability heuristic, anchoring bias
- Suggest connections to existing cognitive psychology notes
```

### 3. 構造化・整理フロー（週次）

```
New Permanent Notes (週の蓄積)
    ↓
AI Structure Analysis
    ├─ Update existing Structure Notes
    ├─ Suggest new Structure Notes
    └─ Update Index Notes
    ↓
Human Validation
    ↓
Structure/Index Updates
```

**AI指示例**:
```
Review this week's 12 new permanent notes in cognitive psychology domain:
- Update ST-cognitive-bias-framework.md
- Suggest new structure notes if needed
- Update IDX-psychology-concepts.md with new entries
```

### 4. プロジェクト統合フロー

```
Project Initiation
    ↓
AI Knowledge Mapping
    ├─ Search relevant permanent notes
    ├─ Identify knowledge gaps
    └─ Create project note template
    ↓
Project Execution & Learning
    ├─ New insights → Permanent notes
    └─ Project progress updates
    ↓
Project Completion
    └─ Archive & extract lessons learned
```

### 5. MCP検索・活用フロー

```
Query from External Tool
    ↓
MCP Server (Obsidian)
    ├─ Search across all note types
    ├─ Follow link connections
    └─ Aggregate relevant information
    ↓
Structured Response
    ├─ Direct facts (Permanent notes)
    ├─ Context (Structure notes)
    └─ Sources (Literature notes)
```

### 日次/週次/月次ルーチン

**Daily (5分)**:
- Notion → Inbox 移行
- 1-2 Literature notes処理

**Weekly (30分)**:
- Structure/Index notes更新
- 新しい接続の発見
- AI提案のレビュー

**Monthly (60分)**:
- システム全体の健康チェック
- 使用されていないノートの確認
- ワークフローの改善

## 重要な構造制約

### ディレクトリ深度制限
**絶対ルール**: 全システムで最大3階層までのディレクトリ深度を維持
- ✅ 正しい: `10-Literature-Notes/books/fiction/`
- ❌ 不正: `10-Literature-Notes/books/fiction/mystery/detective/`
- ✅ 正しい: `30-Structure-Notes/domains/psychology/`
- ❌ 不正: `30-Structure-Notes/domains/psychology/cognitive/biases/`

**理由**:
- システムの保守性確保
- 過度な分類の防止
- 検索効率の維持
- 移行コストの最小化

**実装**:
- 分類が必要な場合はタグやファイル名で対応
- インデックスノートで詳細分類を管理
- 構造ノートで概念階層を表現

このワークフローはいかがでしょうか？実践的な調整点はありますか？