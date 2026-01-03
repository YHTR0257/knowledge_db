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
- `50-Project-Notes/` - Project lifecycle management with dedicated directories
  - `50-Project-Notes/01_active/` - Currently active projects
    - Each project has its own directory: `YYYYMMDD_project-name/`
    - Contains main project note and project-specific subdirectories
  - `50-Project-Notes/02_archived/` - Completed or discontinued projects
    - Same structure as active projects
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
- **Project Notes**: `PRJ_YYYYMMDD_project-name.md`
  - Located within dedicated project directories
  - Project directory format: `YYYYMMDD_project-name/`
  - Directory states: `01_active/` or `02_archived/`
  - Example: `50-Project-Notes/01_active/20241201_job-hunting/PRJ_20241201_job-hunting.md`

All filenames use lowercase with hyphens as separators (underscores for project directories and notes), no spaces or special characters.

#### Project Directory Structure
Each project has a dedicated directory that contains:
- **Main project note**: `PRJ_YYYYMMDD_project-name.md` - The primary project documentation
- **deliverables/** (optional) - Project outputs, final documents, code, presentations
- **reviews/** (optional) - Review notes, feedback sessions, progress assessments
- **resources/** (optional) - Project-specific materials, references, working files

This structure keeps all project-related materials organized in a single location.

### Directory Depth Restriction
**CRITICAL**: Maintain maximum 3-level directory depth throughout the system:
- ✅ Correct: `10-Literature-Notes/books/fiction/`
- ❌ Incorrect: `10-Literature-Notes/books/fiction/mystery/detective/`
- ✅ Correct: `40-Index-Notes/concepts/psychology/`
- ❌ Incorrect: `40-Index-Notes/concepts/psychology/cognitive/biases/`

**Exception**: Project directories (`50-Project-Notes/01_active/YYYYMMDD_project-name/`) may contain flexible internal subdirectories (deliverables/, reviews/, resources/) as needed for project-specific organization. This exception allows projects to maintain their own internal structure without violating the global depth constraint.

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

#### Creating a New Project
1. Create project directory in `50-Project-Notes/01_active/`
2. Use format: `YYYYMMDD_project-name/`
3. Create main project note: `PRJ_YYYYMMDD_project-name.md`
4. Add optional subdirectories as needed:
   - `deliverables/` - For project outputs
   - `reviews/` - For progress reviews and feedback
   - `resources/` - For project-specific materials

#### Project Lifecycle Management
- **Active projects**: Keep in `50-Project-Notes/01_active/YYYYMMDD_project-name/`
- **Archiving**: Move entire project directory to `50-Project-Notes/02_archived/` when:
  - Project is completed
  - Project is discontinued or on indefinite hold
  - Project transitions to maintenance-only mode

#### Linking and Integration
- Link project notes to relevant permanent notes and literature notes
- Update relevant index notes when projects relate to specific concepts or methods
- Cross-reference project deliverables in permanent notes when they contain reusable knowledge

#### Examples
- Active project: `50-Project-Notes/01_active/20241201_job-hunting/PRJ_20241201_job-hunting.md`
- Archived project: `50-Project-Notes/02_archived/20251029_company-analysis/PRJ_20251029_company-analysis.md`
- Project with deliverables: `50-Project-Notes/01_active/20250831_thesis/deliverables/final-paper.pdf`

### Modifying Workflows

When modifying the workflow system:
- Edit `90-Meta/workflows/agent_rules.yml` for process changes
- Maintain confidence thresholds and quality control parameters
- Update templates if structural changes are needed

The system is optimized for knowledge workers who need to process large amounts of information and create a persistent, queryable knowledge base.