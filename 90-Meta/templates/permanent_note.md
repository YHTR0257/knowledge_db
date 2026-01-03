---
id: <% tp.date.now("YYYYMMDDHHmm") %>-<% tp.system.prompt("Concept name (lowercase-with-hyphens)") %>
created: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
tags: [permanent, {{domain}}, {{concept_type}}]
type: permanent-note
status: <% tp.system.suggester(["draft", "reviewed", "mature"], ["draft", "reviewed", "mature"], false, "Select status") %>
confidence: <% tp.system.suggester(["high", "medium", "low"], ["high", "medium", "low"], false, "Select confidence level") %>
source_type: {{source_type}}
---

# Key Points

- {{point1}}
- {{point2}}
- {{point3}}

# Context
{{why_this_matters}}

# Body

%% この概念の中身について記述 %%

## Related Concepts

関係する概念とその関係性について1行で簡潔に記載する

- {{related1}} - {{relationship_type}}
    - {{description1}}
- {{related2}} - {{relationship_type}}
    - {{description2}}

## Sources
- {{Literature_note1}}
- {{Literature_note2}}
- {{WEB_link1}}

<!-- 以下はオプション -->

# 応用例

{{application_examples}}

---
- Generated: {{generation_timestamp}}
- Model: {{ai_model_used}}