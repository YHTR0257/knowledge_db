# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a Zettelkasten-style knowledge management system built for Obsidian, designed to process and organize knowledge from various sources into interconnected atomic notes. The system implements a structured approach to knowledge capture, processing, and retrieval.

## Architecture

### Directory Structure
The repository follows a hierarchical knowledge organization system:

- `00-Inbox/` - Raw content awaiting processing
- `10-Literature-Notes/` - Source material notes (books, articles, videos, conversations)
- `20-Permanent-Notes/` - Atomic, evergreen knowledge units
  - `20-Permanent-Notes/YYYY/` - Permanent notes organized by year for better management
- `30-Structure-Notes/` - Topic and theme organization
- `40-Index-Notes/` - Concept, people, and method indexes
- `50-Project-Notes/` - Active and completed project documentation
  - `50-Project-Notes/501-career/` - Career-related projects (job hunting, internships, learning)
    - `active/` - Currently ongoing career projects
    - `completed/` - Finished career projects
  - `50-Project-Notes/502-artifact/` - Deliverable and artifact projects (analyses, documents)
    - `active/` - Currently ongoing artifact creation
    - `completed/` - Finished artifacts
- `90-Meta/` - System templates, workflows, and configuration

### Core Workflow System

The knowledge processing pipeline is defined in `90-Meta/workflows/agent_rules.yml` and implements six main workflows:

1. **Content Classification** - Automatically categorizes incoming content
2. **Literature Processing** - Extracts permanent notes from literature
3. **Structure Maintenance** - Updates knowledge organization
4. **Index Management** - Maintains cross-references and navigation
5. **Project Integration** - Connects projects to existing knowledge
6. **MCP Query Response** - Handles external knowledge queries

### File Naming Conventions

The system uses strict naming patterns:
- **Permanent Notes**: `YYYYMMDDHHMM-title-in-english.md` or `YYYYMMDD-title-in-english.md`
  - Stored in year subdirectories: `20-Permanent-Notes/YYYY/`
  - Example: `20-Permanent-Notes/2025/20251115-ctc-company-analysis.md`
- **Literature Notes**: `LN-YYYYMMDD-author-lastname-work-title.md`
- **Structure Notes**: `ST-theme-name.md`
- **Index Notes**: `IDX-category-name.md`
- **Project Notes**: `PRJ-YYYYMMDD-project-name.md`
  - Organized by category code (501-career, 502-artifact)
  - Further divided into `active/` and `completed/` subdirectories
  - Example: `50-Project-Notes/501-career/active/PRJ-20241201-job-hunting-strategy.md`

All filenames use lowercase with hyphens as separators, no spaces or special characters.

#### Project Category Codes
- **501**: Career-related projects (job hunting, internships, skill development, thesis)
- **502**: Artifact projects (company analyses, research documents, deliverables)

### Directory Depth Restriction
**CRITICAL**: Maintain maximum 3-level directory depth throughout the system:
- ✅ Correct: `10-Literature-Notes/books/fiction/`
- ❌ Incorrect: `10-Literature-Notes/books/fiction/mystery/detective/`
- ✅ Correct: `40-Index-Notes/concepts/psychology/`
- ❌ Incorrect: `40-Index-Notes/concepts/psychology/cognitive/biases/`

This restriction ensures system maintainability and prevents over-categorization.

## Templates

Two main note templates are available:

### Permanent Note Template (`90-Meta/templates/permanent_note.md`)
- Focuses on atomic concepts with core ideas, context, implications
- Includes sections for counterarguments, applications, and related concepts
- Designed for evergreen, referenceable knowledge units

### Literature Note Template (`90-Meta/templates/literature_note.md`)
- Structured for source material processing
- Includes metadata, arguments, evidence quality assessment
- Links to generated permanent notes and follow-up research

## Key Principles

1. **Atomic Principle**: One concept per permanent note
2. **Connection-Oriented**: Emphasis on bidirectional linking between concepts
3. **Source Attribution**: All knowledge tied back to original sources
4. **Quality Control**: Confidence scoring and validation thresholds
5. **Systematic Processing**: Batch processing with defined intervals

## Working with This Repository

### Adding Content

When adding permanent notes:
- Place raw material in `00-Inbox/` for processing
- Use appropriate templates from `90-Meta/templates/`
- Follow naming conventions strictly
- Store in year subdirectories: `20-Permanent-Notes/YYYY/`
- Create bidirectional links between related concepts
- Update relevant index notes when adding new concepts

### Managing Projects

When creating or updating project notes:
- Choose appropriate category code:
  - `501-career/` for career-related projects
  - `502-artifact/` for deliverable/analysis projects
- Place in `active/` subdirectory during work
- Move to `completed/` subdirectory when finished
- Use format: `PRJ-YYYYMMDD-project-name.md`
- Link project notes to relevant permanent notes and literature notes

Examples:
- Active career project: `50-Project-Notes/501-career/active/PRJ-20241201-job-hunting-strategy.md`
- Completed artifact: `50-Project-Notes/502-artifact/completed/PRJ-20251029-company-analysis.md`

### Modifying Workflows

When modifying the workflow system:
- Edit `90-Meta/workflows/agent_rules.yml` for process changes
- Maintain confidence thresholds and quality control parameters
- Update templates if structural changes are needed

The system is optimized for knowledge workers who need to process large amounts of information and create a persistent, queryable knowledge base.