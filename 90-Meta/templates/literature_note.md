---
id: LN-<% tp.date.now("YYYYMMDD") %>-<% tp.system.prompt("Author lastname-Work title (lowercase-with-hyphens)") %>
created: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
tags: [literature, {{medium}}, {{domain}}, {{quality_tier}}]
type: literature-note
status: <% tp.system.suggester(["processed", "unprocessed", "archived"], ["processed", "unprocessed", "archived"], false, "Select status") %>
rating: {{rating_out_of_5}}
reading_time: {{time_spent}}
confidence: {{confidence_level}}
source_type: {{source_type}}
projects: [{{related_project}}]
permanent_notes: [{{related_permanent_note}}]
---

## Overview

**title** : {{ article title }}

## Key points
<!-- 本記事の keyとなる点を１文で記述し、この記事で重要なことは何かを一目でわかるようにする -->
- 
- 

## Back grounds

背景

<!-- この記事の背景を簡潔に記述する -->

- {{background1}}
    - この背景について1行で簡潔に説明する
- {{background2}}
    - この背景について1行で簡潔に説明する

## Conclusion
結論
本記事においてどのような結論が導き出されたのか

## Methods

方法論
<!-- 本記事で用いられた方法論やアプローチを記載する -->

確認手法はどのようなものなのか

## Discussion

この記事を受けて自分にどのような影響をもたらすのか

## Questions
<!-- 次に抱いた疑問や課題を記述する -->

- {{question1}}
    - この疑問について1行で簡潔に説明する
- {{question2}}
    - この疑問について1行で簡潔に説明する

## Reference

- Author : {{author_name1}}, {{author_name2}}
- Year : {{publication_year}}
- Title : {{paper_title}}
- Publisher : {{publisher_name}}
- Journal : {{journal_name}}
- DOI : {{doi_number}}
