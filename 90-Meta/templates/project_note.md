---
id: PRJ-<% tp.date.now("YYYYMMDDHHmm") %>
created: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
priority: <% tp.system.suggester(["high", "medium", "low"], ["high", "medium", "low"], false, "Select priority") %>
deadline: <% tp.system.prompt("Deadline (YYYY-MM-DD)") %>
tags: [project, <% tp.system.prompt("Project category (501-career/502-artifact/503-skill-building)") %>, <% tp.system.prompt("Project focus area") %>]
type: project-note
status: <% tp.system.suggester(["active", "completed", "on-hold", "cancelled"], ["active", "completed", "on-hold", "cancelled"], false, "Select status") %>
category_code: <% tp.system.suggester(["501", "502", "503"], ["501-career", "502-artifact", "503-skill-building"], false, "Select category") %>
---

# <% tp.system.prompt("Project Title") %>

## Project Overview(プロジェクト概要)
{{project_description}}

<!-- プロジェクトを一言で表す簡潔で明確な説明を記載します。 -->

## Objectives (目的)

<!-- このプロジェクトの目的を記述するためのセクションです。各目的に対して、達成するための具体的なステップも記載します。SMARTなSuccess Criteriaを設定が必要不可欠です。 -->

| Objective (目的) | Description (説明) | Success Criteria (成功基準) |
| :------- | :---------- | :--------------- |
| {{objective1}} | {{description1}} | {{success_criteria1}} |
| {{objective2}} | {{description2}} | {{success_criteria2}} |
| {{objective3}} | {{description3}} | {{success_criteria3}} |

## Timeline (タイムライン)

<!-- 詳細はgoogleスプレッドシートなどを用いて整理することを推奨します。 -->

| Phase | Start Date | End Date | Milestones | Daily Reports |
| :--- | :--- | :--- | :------ | :--- |
| Phase 1 | {{phase1_start}} | {{phase1_end}} | - {{Milestone1}}<br>- {{Milestone2}}<br>- {{Milestone3}} | 
| Phase 2 | {{phase2_start}} | {{phase2_end}} | - {{Milestone1}}<br>- {{Milestone2}}<br>- {{Milestone3}} |
| Phase 3 | {{phase3_start}} | {{phase3_end}} | - {{Milestone1}}<br>- {{Milestone2}}<br>- {{Milestone3}} |

- Phase1: {{phase1_description}}
    - !{YYYY-MM-DD} Diary Entry Title 1
    - !{YYYY-MM-DD} Diary Entry Title 2
- Phase2: {{phase2_description}}
    - !{YYYY-MM-DD} Diary Entry Title 1
    - !{YYYY-MM-DD} Diary Entry Title 2
- Phase3: {{phase3_description}}
    - !{YYYY-MM-DD} Diary Entry Title 1
    - !{YYYY-MM-DD} Diary Entry Title 2

## Risk Management (リスク管理)

### High Risk: {{high_risk_name}}
**対策**:
- {{high_risk_mitigation1}}
- {{high_risk_mitigation2}}

### Medium Risk: {{medium_risk_name}}
**対策**:
- {{medium_risk_mitigation1}}

### Low Risk: {{low_risk_name}}
**対策**:
- {{low_risk_mitigation1}}


## Related Knowledge Base (関連知識ベース)

### Related Projects
- [[{{related_project1}}]] - {{relationship_description1}}
- [[{{related_project2}}]] - {{relationship_description2}}

### Related Permanent Notes
- [[{{permanent_note1}}]] - {{concept_relation1}}
- [[{{permanent_note2}}]] - {{concept_relation2}}

### Related Literature Notes
- [[{{literature_note1}}]] - {{source_relation1}}
- [[{{literature_note2}}]] - {{source_relation2}}

### Related Index Notes
- [[{{index_note1}}]] - {{index_relation1}}

## Review and Evaluation (振り返りと評価)

<!-- プロジェクトの振り返りを行うためのセクションです。各フェーズ終了後に評価を行い、次のフェーズに向けた改善点を明確にします。 -->

### Phase 1 Review
{{phase1_review}}

### Phase 2 Review
{{phase2_review}}

### Phase 3 Review
{{phase3_review}}

### Final Evaluation
{{final_evaluation}}

---

**Project Start**: <% tp.date.now("YYYY-MM-DD") %>
**Target Completion**: {{target_completion_date}}
**Last Updated**: <% tp.date.now("YYYY-MM-DD") %>
**Review Frequency**: {{review_frequency}}
