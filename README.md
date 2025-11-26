# Hugging Face Skills

This repository contains skills for your coding agent to use when working with Hugging Face. It's compatible Claude Code, Google Gemini, and OpenAI Codex.

Skills are self-contained folders that package instructions, scripts, and resources. Each folder includes a `SKILL.md` file with YAML frontmatter (name and description) followed by the guidance your coding agent follows while the skill is active. 

> [!NOTE]
> 'Skills' is actually an Anthropic term used within Claude Code and not adopted by other agent tools, but we love it! OpenAI Codex uses an `AGENTS.md` file to define the instructions for your coding agent. Google Gemini uses 'extensions' to define the instructions for your coding agent in a `gemini-extension.json` file.

--- 

## Humanity's Last Hackathon (of 2025)

<img src="assets/banner.png" alt="Humanity's Last Hackathon (of 2025)" width="100%">

**December 2025** — We're kicking off this skills library with a community hackathon. Use coding agents to ship real contributions: evaluate models, build datasets, fine-tune LLMs, and earn XP on a community leaderboard.

| Week | Quest | Skill |
|------|-------|-------|
| 1 | [Evaluate a Hub Model](quests/02_evaluate-hub-model.md) | `hf_model_evaluation/` |
| 2 | [Publish a Hub Dataset](quests/03_publish-hub-dataset.md) | `hf_dataset_creator/` |
| 3 | [Supervised Fine-Tuning](quests/04_sft-finetune-hub.md) | `hf-llm-trainer/` |

**Get started:** [Join the hackathon org](https://huggingface.co/organizations/hf-skills/share/KrqrmBxkETjvevFbfkXeezcyMbgMjjMaOp) → Read [quests/01_start.md](quests/01_start.md) → Pick a quest

---

## Installation

Hugging Face skills are compatible with Claude Code, Codex, and Gemini CLI. With integrations Cursor, Windsurf, and Continue, on the way.

### Claude Code

1. Register the repository as a plugin marketplace:  
   ```
   /plugin marketplace add huggingface/skills
   ```

2. In Claude Code, open  
   **Browse and install plugins** → **huggingface-skills**  
   (the marketplace you just added).

3. Choose the folder you want  
   (`hf-llm-trainer`, `hf_model_evaluation`, `hf_dataset_creator`, or `hf-paper-publisher`)  
   and select **Install now**.

4. Prefer commands?  
   After registering the marketplace, run:  
   ```
   /plugin install <skill-folder>@huggingface-skills
   ```
   For example:  
   ```
   /plugin install hf-llm-trainer@huggingface-skills
   ```

### Codex

1. Copy or symlink this repository’s `AGENTS.md` file to your Codex profile:  
   (default location:  
   ```
   ~/.codex/AGENTS.md
   ```
   )  
   Or, set  
   ```
   CODEX_HOME=$(pwd)/.codex
   ```
   in your workspace to use a project-specific file.

2. After updating `AGENTS.md`, run:  
   ```
   codex --ask-for-approval never "Summarize the current instructions."
   ```
   to verify the instructions are loaded.

3. For more details, see the [Codex AGENTS guide](https://developers.openai.com/codex/guides/agents-md).

### Gemini CLI

1. This repo includes `gemini-extension.json`.  
   Use it as a Gemini CLI extension.

2. Install locally:  
   ```
   gemini extensions install . --consent
   ```
   or use the GitHub URL.

3. Restart Gemini CLI.  
   The extension appears as `huggingface-skills` in  
   ```
   ~/.gemini/extensions
   ```
   Run:  
   ```
   gemini extensions list
   ```
   to verify.

4. See [Gemini CLI extensions docs](https://geminicli.com/docs/extensions/#installing-an-extension) for more help.

## Skills

This repository contains a few skills to get you started. You can also contribute your own skills to the repository.

### Available skills

| Skill Folder            | Description                                                                                                                | Documentation                                          |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------|
| `hf_dataset_creator/`   | Prompts, templates, and scripts for creating structured training datasets.                                                 | [SKILL.md](hf_dataset_creator/SKILL.md)                |
| `hf_model_evaluation/`  | Instructions plus utilities for orchestrating evaluation jobs, generating reports, and mapping metrics.                    | [SKILL.md](hf_model_evaluation/SKILL.md)               |
| `hf-llm-trainer/`       | Comprehensive training skill with `SKILL.md` guidance, helper scripts (e.g., `train_sft_example.py`, `convert_to_gguf.py`, cost estimators). | [SKILL.md](hf-llm-trainer/SKILL.md)                    |
| `hf-paper-publisher/`   | Tools for publishing and managing research papers on Hugging Face Hub. Index papers from arXiv, link papers to models/datasets, generate professional research articles from templates, and manage paper authorship. | [SKILL.md](hf-paper-publisher/SKILL.md)                |

### Using skills in your coding agent

Once a skill is installed, mention it directly while giving your coding agent instructions:

- "Use the HF LLM trainer skill to estimate the GPU memory needed for a 70B model run."
- "Use the HF model evaluation skill to launch `run_eval_job.py` on the latest checkpoint."
- "Use the HF dataset creator skill to draft new few-shot classification templates."
- "Use the HF paper publisher skill to index my arXiv paper and link it to my model."

Your coding agent automatically loads the corresponding `SKILL.md` instructions and helper scripts while it completes the task.

### Contribute or customize a skill

1. Copy one of the existing skill folders (for example, `hf_dataset_creator/`) and rename it.
2. Update the new folder’s `SKILL.md` frontmatter:
   ```markdown
   ---
   name: my-skill-name
   description: Describe what the skill does and when to use it
   ---

   # Skill Title
   Guidance + examples + guardrails
   ```
3. Add or edit supporting scripts, templates, and documents referenced by your instructions.
4. Reinstall or reload the skill bundle in your coding agent so the updated folder is available.

### Additional references
- Browse the latest instructions, scripts, and templates directly at [huggingface/skills](https://github.com/huggingface/skills).
- Review Hugging Face documentation for the specific libraries or workflows you reference inside each skill.
