---
name: obsidian-vault
description: Expert guidance for reading and writing markdown files in Obsidian with proper frontmatter, backlinks, tags, and note structure.
---

# Obsidian Vault Skill

Expert guidance for reading and writing markdown files in Obsidian with proper frontmatter, backlinks, tags, and note structure.

## Vault Location

**Default Path:** `~/Documents/ObsidianVault`

> **Configuration:** Set your vault path in the `vaultPath` field of `skill.json` or use the environment variable `OBSIDIAN_VAULT_PATH`.

For this installation: `C:\Users\Keshav\Documents\ObsidianVault`

---

## Frontmatter (YAML)

Every note should start with YAML frontmatter between `---` delimiters:

```markdown
---
title: Note Title
created: 2024-01-15
modified: 2024-01-15
tags:
  - tag1
  - tag2
aliases:
  - alternate name
  - another alias
status: draft | in-progress | complete
type: note | project | meeting | daily | reference
---
```

### Required Fields

| Field | Description | Example |
|-------|-------------|---------|
| `title` | Human-readable title | `"Weekly Review Process"` |
| `created` | Creation date (YYYY-MM-DD) | `2024-01-15` |
| `tags` | List of categorization tags | `[productivity, workflow]` |

### Optional Fields

| Field | Description | Example |
|-------|-------------|---------|
| `modified` | Last modified date | `2024-01-20` |
| `aliases` | Alternative names for linking | `["WR", "Weekly Review"]` |
| `status` | Note completion status | `draft`, `complete` |
| `type` | Note category | `project`, `meeting`, `reference` |
| `project` | Associated project | `"[[Project Name]]"` |
| `source` | Original source URL/reference | `"https://..."` |
| `author` | Content author | `"John Smith"` |

### Frontmatter Examples

**Daily Note:**
```yaml
---
title: "2024-01-15"
created: 2024-01-15
tags:
  - daily
type: daily
---
```

**Project Note:**
```yaml
---
title: Client Onboarding Automation
created: 2024-01-15
modified: 2024-01-20
tags:
  - project
  - automation
  - n8n
status: in-progress
type: project
---
```

**Meeting Note:**
```yaml
---
title: "Meeting: Q1 Planning"
created: 2024-01-15
tags:
  - meeting
  - planning
type: meeting
attendees:
  - "[[John Smith]]"
  - "[[Jane Doe]]"
---
```

---

## Backlinks (Internal Links)

### Basic Linking

```markdown
Link to a note: [[Note Name]]
Link with display text: [[Note Name|Display Text]]
Link to heading: [[Note Name#Heading]]
Link to block: [[Note Name#^block-id]]
```

### Linking Patterns

**Direct Reference:**
```markdown
See [[Project Management]] for details.
```

**Aliased Link:**
```markdown
The [[Client Onboarding Automation|onboarding system]] handles this.
```

**Section Link:**
```markdown
Refer to [[Meeting Notes#Action Items]] for tasks.
```

### Embed Content

```markdown
Embed entire note: ![[Note Name]]
Embed heading section: ![[Note Name#Heading]]
Embed image: ![[image.png]]
Embed with size: ![[image.png|500]]
```

### When to Create Links

1. **People:** `[[John Smith]]` - Link to person notes
2. **Projects:** `[[Project Name]]` - Link to project notes
3. **Concepts:** `[[Concept]]` - Link to reference/definition notes
4. **Meetings:** `[[Meeting: Topic]]` - Link to meeting notes
5. **Dates:** `[[2024-01-15]]` - Link to daily notes

### Link Best Practices

- Create links even if the note doesn't exist yet (creates future connection)
- Use consistent naming: `[[Person Name]]` not `[[person-name]]`
- Prefer full names over abbreviations for clarity
- Use aliases when the link text should differ from note name

---

## Tags

### Tag Syntax

```markdown
Inline tag: #tag-name
Nested tag: #category/subcategory
In frontmatter:
tags:
  - tag1
  - category/subcategory
```

### Tag Hierarchy

```
#project
#project/active
#project/completed
#project/on-hold

#area
#area/work
#area/personal
#area/health

#type
#type/meeting
#type/reference
#type/daily

#status
#status/todo
#status/in-progress
#status/done
```

### Tags vs Links

| Use Tags For | Use Links For |
|--------------|---------------|
| Categories/types | Specific notes |
| Status indicators | People |
| Broad topics | Projects |
| Filtering/searching | Concepts with their own notes |

---

## Note Structure

### Standard Note Template

```markdown
---
title: Note Title
created: {{date}}
tags:
  - relevant-tag
type: note
---

# Note Title

Brief summary or context (1-2 sentences).

## Overview

Main content explaining the topic.

## Key Points

- Point 1
- Point 2
- Point 3

## Details

Detailed information, examples, or explanations.

## Related

- [[Related Note 1]]
- [[Related Note 2]]

## References

- [External Link](https://example.com)
- Source material
```

### Daily Note Template

```markdown
---
title: "{{date:YYYY-MM-DD}}"
created: {{date:YYYY-MM-DD}}
tags:
  - daily
type: daily
---

# {{date:dddd, MMMM D, YYYY}}

## Tasks

- [ ] Task 1
- [ ] Task 2

## Notes

### Morning


### Afternoon


### Evening


## Gratitude

1.
2.
3.

## Links Created Today

-
```

### Meeting Note Template

```markdown
---
title: "Meeting: {{title}}"
created: {{date}}
tags:
  - meeting
type: meeting
attendees:
  - "[[Person 1]]"
  - "[[Person 2]]"
project: "[[Related Project]]"
---

# Meeting: {{title}}

**Date:** {{date}}
**Attendees:** [[Person 1]], [[Person 2]]

## Agenda

1. Topic 1
2. Topic 2

## Discussion

### Topic 1


### Topic 2


## Decisions

- Decision 1
- Decision 2

## Action Items

- [ ] Action 1 - [[Assignee]] - Due: {{date}}
- [ ] Action 2 - [[Assignee]] - Due: {{date}}

## Next Meeting


```

### Project Note Template

```markdown
---
title: Project Name
created: {{date}}
modified: {{date}}
tags:
  - project
  - project/active
type: project
status: in-progress
---

# Project Name

## Overview

Brief description of the project.

## Goals

1. Goal 1
2. Goal 2

## Tasks

- [ ] Task 1
- [ ] Task 2

## Timeline

| Milestone | Date | Status |
|-----------|------|--------|
| Start | {{date}} | Done |
| Phase 1 | | |
| Completion | | |

## Notes

### {{date}}


## Related

- [[Related Project]]
- [[Person Involved]]

## Resources

- [Link](url)
```

---

## Folder Structure

```
ObsidianVault/
├── 00-Inbox/              # New notes, unsorted
├── 01-Daily/              # Daily notes (YYYY-MM-DD.md)
├── 02-Projects/           # Active projects
│   ├── Project-A/
│   └── Project-B/
├── 03-Areas/              # Ongoing areas of responsibility
│   ├── Work/
│   ├── Personal/
│   └── Health/
├── 04-Resources/          # Reference material
│   ├── People/
│   ├── Concepts/
│   └── Templates/
├── 05-Archive/            # Completed/inactive items
├── Attachments/           # Images, PDFs, etc.
└── Templates/             # Note templates
```

---

## File Naming Conventions

| Type | Format | Example |
|------|--------|---------|
| Daily notes | `YYYY-MM-DD.md` | `2024-01-15.md` |
| Meeting notes | `Meeting - Topic YYYY-MM-DD.md` | `Meeting - Q1 Planning 2024-01-15.md` |
| Project notes | `Project Name.md` | `Client Onboarding Automation.md` |
| People notes | `First Last.md` | `John Smith.md` |
| Concepts | `Concept Name.md` | `Zettelkasten Method.md` |

---

## Reading Notes

When reading notes from the vault:

```javascript
// Read a note
Read("C:/Users/Keshav/Documents/ObsidianVault/01-Daily/2024-01-15.md")

// Find notes by pattern
Glob("C:/Users/Keshav/Documents/ObsidianVault/**/*.md")

// Search for content
Grep("pattern", "C:/Users/Keshav/Documents/ObsidianVault")
```

---

## Writing Notes

### Creating a New Note

1. Determine the appropriate folder based on note type
2. Use proper file naming convention
3. Include complete frontmatter
4. Add links to related notes
5. Use appropriate tags

### Example: Create Daily Note

```markdown
---
title: "2024-01-15"
created: 2024-01-15
tags:
  - daily
type: daily
---

# Monday, January 15, 2024

## Tasks

- [ ] Review project status
- [ ] Team meeting at 2pm

## Notes

Started work on the [[Client Onboarding Automation]] project.
Met with [[John Smith]] to discuss requirements.

## Links Created Today

- [[Client Onboarding Automation]]
```

### Example: Create Project Note

```markdown
---
title: Website Redesign
created: 2024-01-15
modified: 2024-01-15
tags:
  - project
  - project/active
  - web
type: project
status: in-progress
---

# Website Redesign

## Overview

Complete redesign of company website with focus on conversion optimization.

## Goals

1. Improve page load speed by 50%
2. Increase conversion rate by 25%
3. Mobile-first responsive design

## Tasks

- [ ] Audit current site performance
- [ ] Create wireframes
- [ ] Design mockups
- [ ] Develop new templates

## Related

- [[Marketing Strategy]]
- [[Jane Doe]] - Designer
- [[John Smith]] - Developer
```

---

## Common Operations

### Find All Notes Mentioning a Topic

```bash
# Search for notes containing "project"
Grep("project", "C:/Users/Keshav/Documents/ObsidianVault", {type: "md"})
```

### List Recent Daily Notes

```bash
Glob("C:/Users/Keshav/Documents/ObsidianVault/01-Daily/2024-*.md")
```

### Find Notes with Specific Tag

```bash
Grep("tags:.*automation", "C:/Users/Keshav/Documents/ObsidianVault")
```

### Find Backlinks to a Note

```bash
Grep("\\[\\[Note Name\\]\\]", "C:/Users/Keshav/Documents/ObsidianVault")
```

---

## Quick Reference

### Frontmatter Template
```yaml
---
title:
created: YYYY-MM-DD
tags:
  -
type: note
---
```

### Link Syntax
```markdown
[[Note]]                    # Basic link
[[Note|Text]]               # Aliased link
[[Note#Heading]]            # Section link
![[Note]]                   # Embed
![[image.png|500]]          # Sized image
```

### Task Syntax
```markdown
- [ ] Incomplete task
- [x] Completed task
- [ ] Task with [[link]] and #tag
- [ ] Task due on [[2024-01-20]]
```

### Callouts
```markdown
> [!note] Title
> Content

> [!tip] Tip
> Helpful information

> [!warning] Warning
> Important caution

> [!info] Info
> Additional context
```

---

## Project Tracking

### Projects Dashboard

Location: `02-Projects/Projects Dashboard.md`

The dashboard provides an overview of all projects with status tracking.

### Project Status Values

Use these in frontmatter `status` field:

| Status | Emoji | Description |
|--------|-------|-------------|
| `not-started` | ⚪ | Planned but not begun |
| `in-progress` | 🟡 | Actively working on |
| `on-hold` | 🟠 | Paused temporarily |
| `blocked` | 🔴 | Waiting on dependency |
| `complete` | 🟢 | Finished |

### Project Frontmatter

```yaml
---
title: Project Name
created: YYYY-MM-DD
modified: YYYY-MM-DD
tags:
  - project
  - project/active
type: project
status: in-progress
priority: high | medium | low
---
```

### Creating a New Project

1. Create note in `02-Projects/` folder
2. Use frontmatter with status and priority
3. Add link to [[Projects Dashboard]]
4. Update dashboard table with new project

### Updating Project Status

1. Change `status` field in frontmatter
2. Update `modified` date
3. Move project row in dashboard to appropriate section
4. Add note in project's Notes section explaining change

### Finding Projects by Status

```bash
# Find all active projects
Grep("status: in-progress", "~/Documents/ObsidianVault/02-Projects")

# Find blocked projects
Grep("status: blocked", "~/Documents/ObsidianVault/02-Projects")

# Find all project files
Glob("~/Documents/ObsidianVault/02-Projects/*.md")
```

---

## Related Skills

- **subagent-orchestrator**: Use Documentation Agent to generate notes automatically
- **n8n-expression-syntax**: Build n8n workflows that process Obsidian notes
- **n8n-mcp-tools-expert**: Create automation workflows for note management
