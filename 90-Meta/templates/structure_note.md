---
id: ST-<% tp.system.prompt("Theme name (lowercase-with-hyphens)") %>
created: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
tags: [structure, {{domain}}, {{subdomain}}]
type: structure-note
status: <% tp.system.suggester(["active", "archived"], ["active", "archived"], false, "Select status") %>
scope: {{scope}}
---

# <% tp.system.prompt("Structure Title") %>

## Overview
{{overview_description}}

## Concept Hierarchy
### Level 1: 基礎的な概念
- {{concept1}} - {{description1}}
    - {{note1}} - {{description1}}
    - {{note2}} - {{description2}}
- {{concept2}} - {{description2}}

### Level 2: 具体的な応用 
- {{application1}} - {{description1}}
- {{application2}} - {{description2}}

### Level 3: 高度なトピック
- {{advanced1}} - {{description1}}
- {{advanced2}} - {{description2}}

## Key Relationships
{{relationship_description}}

## Learning Path
1. Start with: {{entry_point}}
2. Then explore: {{next_steps}}
3. Advanced: {{advanced_topics}}

## Connected Structures
- {{related_structure1}}
- {{related_structure2}}

---