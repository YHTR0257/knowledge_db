---
id: IDX-<% tp.system.prompt("Category name (lowercase-with-hyphens)") %>
created: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
tags: [index, {{category}}, {{scope}}]
type: index-note
status: <% tp.system.suggester(["active", "archived"], ["active", "archived"], false, "Select status") %>
coverage: {{coverage_percentage}}
entry_level: <% tp.system.suggester(["beginner", "intermediate", "advanced"], ["beginner", "intermediate", "advanced"], false, "Select entry level") %>
---

# Index: <% tp.system.prompt("Category Name") %>

## Category Definition
{{what_this_category_covers}}

## Entry Points
### For Beginners
- {{beginner_note1}} - {{brief_description}}
- {{beginner_note2}} - {{brief_description}}

### For Intermediate
- {{intermediate_note1}} - {{brief_description}}
- {{intermediate_note2}} - {{brief_description}}

### For Advanced
- {{advanced_note1}} - {{brief_description}}
- {{advanced_note2}} - {{brief_description}}

## Core Concepts
### {{subcategory1}}
- {{concept1}} - {{note_id}}
- {{concept2}} - {{note_id}}
- {{concept3}} - {{note_id}}

### {{subcategory2}}
- {{concept4}} - {{note_id}}
- {{concept5}} - {{note_id}}

### {{subcategory3}}
- {{concept6}} - {{note_id}}
- {{concept7}} - {{note_id}}

## Key People
- {{person1}} - {{contribution}}
- {{person2}} - {{contribution}}

## Important Methods
- {{method1}} - {{application_area}}
- {{method2}} - {{application_area}}

## Related Indexes
- {{related_index1}} - {{relationship}}
- {{related_index2}} - {{relationship}}

## Missing Areas
- {{gap1}} - {{priority_level}}
- {{gap2}} - {{priority_level}}

## Statistics
- Total notes: {{note_count}}
- Last major update: {{last_update}}
- Growth rate: {{new_notes_per_month}}

---
Template optimized for comprehensive knowledge mapping