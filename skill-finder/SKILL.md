---
name: skill-finder
description: Search for and install agent skills from Hugging Face. Use this skill when users want to discover new skills, browse available skills on the Hub, or install skills to extend agent capabilities.
---

# Skill Finder: Discover and Install Agent Skills from Hugging Face

## Overview

Agent skills are packaged instructions and resources hosted on Hugging Face Spaces with the `agent-skill` tag. Use the Hugging Face MCP tools to search and install them.

## Searching for Skills

Use the `hf_space_search` MCP tool to find skills:

```python
# Search for all skills
hf_space_search(filter="agent-skill")

# Search with keywords
hf_space_search(filter="agent-skill", search="training")
```

## Installing Skills

Use `git clone` to download a skill Space from the Hugging Face Hub:


```python
# Clone a skill Space
# (Spaces are hosted under https://huggingface.co/spaces/<org>/<space>)
git clone https://huggingface.co/spaces/hf-skills/llm-trainer ".<agent_name>/skills/llm-trainer"
```

`<agent-name>` is the name of the agent you are installing the skill for. For example, for 'codex', the local_dir would be '.codex/skills/llm-trainer', and for 'claude', the local_dir would be '.claude/skills/llm-trainer'.

## Skill Structure

A valid skill contains a `SKILL.md` file with YAML front matter:

```markdown
---
name: my-skill-name
description: When to use this skill and what it does.
---

# Instructions for the agent...
```

Optional directories: `scripts/`, `references/`, `templates/`
