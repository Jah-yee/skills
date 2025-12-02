# Adding Evaluation Results to Hugging Face Models with AI Coding Agents

This tutorial shows you how to add structured evaluation results to Hugging Face model cards using the `hf_model_evaluation` skill with an AI coding agent like Claude Code.

> [!NOTE]
> **What's an AI Coding Agent?**
> AI coding agents like Claude Code, Cursor, or Windsurf can use "skills"—packaged resources containing instructions, scripts, and domain knowledge—to accomplish specialized tasks. This skill handles the complexity of extracting benchmark data and formatting it correctly for the Hugging Face Hub.

---

## Why Use a Skill for This?

Model evaluation metadata is one of those tasks that sounds simple but has surprising edge cases:

- Markdown tables come in many formats (comparison tables, transposed tables, merged cells)
- The `model-index` YAML format has specific requirements
- You need to avoid creating duplicate PRs
- Values need to be verified against the source

The `hf_model_evaluation` skill handles all of this. Instead of memorizing CLI flags and YAML structures, you describe what you want in natural language and the agent figures out the rest.

---

## Prerequisites

Before starting, make sure you have:

- **Python 3.10+** (3.13 recommended)
- **[uv](https://docs.astral.sh/uv/)** for running Python scripts with auto-managed dependencies
- **A Hugging Face account** with a [write-access token](https://huggingface.co/settings/tokens)
- **An AI coding agent** with the `hf_model_evaluation` skill installed

Set up your environment variables:

```bash
# Required for all methods
export HF_TOKEN=hf_your_write_access_token_here

# Required for Artificial Analysis imports (Method 2)
export AA_API_KEY=your_artificial_analysis_api_key
```

Or create a `.env` file in your working directory—the scripts load it automatically.

## Quick Start: Your First Evaluation Update

Let's walk through the most common scenario: a model has benchmark tables in its README but no structured `model-index` metadata.

> [!NOTE]
> In general, you can one-shot prompt the model to perform the evaluation task and open a pull request on the model repo with:
>
> ```
> Evaluate the model `allenai/Olmo-3-32B-Think` and open a pull request with the evaluation results.
> ```
>
> However, for the sake of education we will walk through an interactive and guided process of prompting. 

### Step 1: Find a Model

Ask your agent to help you find a candidate:

```
Find a trending model on Hugging Face that has benchmark tables
in the README but no model-index metadata.
```

Or if you already have a specific model in mind:

```
Check if allenai/Olmo-3-32B-Think has evaluation tables I can extract.
```

### Step 2: Inspect the Tables

Once you have a model, ask the agent to analyze its README:

```
Inspect the evaluation tables in allenai/Olmo-3-32B-Think
```

The agent will run the inspection and show you something like:

```
======================================================================
Tables found in README for: meta-llama/Llama-3.3-70B-Instruct
======================================================================

## Table 1
   Format: comparison
   Rows: 8

   Columns (4):
      [1] Llama-3.3-70B  ✓ EXACT MATCH
      [2] Llama-3.1-70B
      [3] Llama-3.1-405B

   Sample rows (first column):
      - MMLU
      - HumanEval
      - GSM8K
      - HellaSwag
      - ARC-C
======================================================================
```

This tells you:
- **Table 1** contains evaluation data
- It's a **comparison table** with multiple models as columns
- **Column 1** matches the model name (marked with `✓ EXACT MATCH`)
- The benchmarks include MMLU, HumanEval, GSM8K, etc.

### Step 3: Preview the Extraction

Before making any changes, preview what will be generated:

```
Extract the evaluation data from table 1 and show me the YAML
that would be added to the model card. Don't apply changes yet.
```

The agent will show you the generated `model-index`:

```yaml
model-index:
- name: Llama-3.3-70B-Instruct
  results:
  - task:
      type: text-generation
    dataset:
      name: Benchmarks
      type: benchmark
    metrics:
    - name: MMLU
      type: mmlu
      value: 86.0
    - name: HumanEval
      type: humaneval
      value: 88.4
    - name: GSM8K
      type: gsm8k
      value: 95.8
    source:
      name: Model README
      url: https://huggingface.co/meta-llama/Llama-3.3-70B-Instruct
```

**Always verify** these values against the original README table before proceeding.

### Step 4: Apply the Changes

If you own the model, you can push directly:

```
Apply the evaluation extraction to my-username/my-model
```

For models you don't own, create a pull request:

```
Create a PR to add evaluation metadata to meta-llama/Llama-3.3-70B-Instruct
```

> [!IMPORTANT]
> The agent will automatically check for existing open PRs before creating a new one to avoid duplicates.

## Three Methods for Adding Evaluations

The HF Skill for evaluation can do more than just extract markdown tables. It can collect evaluations results from third party sources like [Artificial Analysis](https://artificialanalysis.ai) and run your own evaluations using [inspect-ai](https://github.com/UKGovernmentBEIS/inspect_ai) on Hugging Face Jobs. Let's explore those in detail.

### Method 1: Extract from README Tables

This is what we just did. Use it when the model has benchmark tables in the README.

**Example prompts:**

```
Extract evaluation tables from deepseek-ai/DeepSeek-V3 and create a PR
```

```
The model Qwen/Qwen2.5-72B-Instruct has benchmarks in a comparison
table. Extract only the scores for Qwen2.5-72B-Instruct.
```

```
Inspect tables in allenai/OLMo-3-8B-Instruct, then extract
the evaluation data from table 2.
```

**Handling tricky table formats:**

For comparison tables with multiple models:
```
Extract from table 1, using column index 2 for the model scores
```

For transposed tables (models as rows, benchmarks as columns):
```
The table has models as rows. Extract the row for "OLMo-3-32B"
```

> [!TIP]
> This method is most useful for 'cleanup'. Either if your model author looking to share your models' evaluation scores or a community member eager to push the needle on open source AI.

### Method 2: Import from Artificial Analysis

[Artificial Analysis](https://artificialanalysis.ai) benchmarks popular models using standardized methodology. If your model is there, you can import scores directly. 

> [!WARNING]
> Artificial Analysis shares scores for both proprietary models like the Claude series as well as open models like the Qwen Series. Which is useful for comparison, but proprietary models do not have Hugging Face repositories that you can add evaluation scores to 😢.

**Example prompts:**

```
Import benchmark scores for Claude Sonnet 4 from Artificial Analysis
and add them to anthropic/claude-sonnet-4
```

```
Check if Qwen3/Qwen3-0.6B has scores on Artificial Analysis, and if so, create a PR to add them
```

The agent will use the AA API to fetch scores and format them correctly:

```yaml
model-index:
- name: claude-sonnet-4
  results:
  - task:
      type: text-generation
    dataset:
      name: Artificial Analysis Benchmarks
      type: artificial_analysis
    metrics:
    - name: MMLU
      type: mmlu
      value: 88.7
    - name: GPQA
      type: gpqa
      value: 65.2
    source:
      name: Artificial Analysis
      url: https://artificialanalysis.ai/models/anthropic/claude-sonnet-4
```

> [!WARNING]
> Artificial Analysis uses a custom naming convention for models that is different to the hub. In most cases, the coding agent is able to align between models, but you should validate this to be sur.

### Method 3: Run Your Own Evaluations

For models without existing benchmarks, you can run evaluations yourself using [inspect-ai](https://github.com/UKGovernmentBEIS/inspect_ai) on Hugging Face Jobs and/or Inference Providers.

**Example prompts:**

```
Run MMLU evaluation on the openai/gpt-oss-20b model
```

```
Submit a GSM8K benchmark job for my-org/custom-model with
--limit 100 for testing
```

The agent will submit the job:

```bash
hf jobs uv run hf_model_evaluation/scripts/inspect_eval_uv.py \
  --flavor t4-small \
  --secret HF_TOKEN=$HF_TOKEN \
  -- --model "meta-llama/Llama-3.1-8B-Instruct" \
     --task "mmlu"
```

> [!NOTE]
> Running your own evaluations takes time (minutes to hours depending on model size and benchmark) and costs money for GPU jobs. Start with `--limit 100` to test your setup.

## Some Common Workflows

Let's explore some common workflows that you might use this skill for.

### Quick Update for Your Own Model

```
I just published my-username/awesome-7b with benchmark tables
in the README. Extract them and update the model card.
```

The agent will:
1. Inspect the tables
2. Preview the YAML
3. Apply changes directly (since you own the model)
4. Validate the result

### Contributing to Community Models

```
Help me contribute evaluation metadata to trending models.
Find 3 models with README benchmarks but no model-index.
```

For each model:
```
Check for existing PRs on organization/popular-model, then
create a PR if none exist
```

The agent will:
1. Check for open PRs first (to avoid duplicates)
2. Inspect tables and preview extraction
3. Create a PR with a helpful description
4. Provide the PR URL so you can track it

### Comprehensive Coverage

```
I want to add both README benchmarks AND Artificial Analysis
scores to my-org/hybrid-model
```

The agent will:
1. Extract from README first
2. Import from AA (merging with existing data)
3. Show the combined results

## Behind the Scenes: The Scripts

The skill uses Python scripts that you can also run directly if needed. The agent calls these automatically, but here's what's happening under the hood:

| What You Ask | Script the Agent Runs |
|--------------|----------------------|
| "Inspect tables" | `uv run scripts/evaluation_manager.py inspect-tables --repo-id "..."` |
| "Extract evaluations" | `uv run scripts/evaluation_manager.py extract-readme --repo-id "..." --table N` |
| "Import from AA" | `uv run scripts/evaluation_manager.py import-aa --creator-slug "..." --model-name "..."` |
| "Check for PRs" | `uv run scripts/evaluation_manager.py get-prs --repo-id "..."` |
| "Validate metadata" | `uv run scripts/evaluation_manager.py validate --repo-id "..."` |

You can run these directly for debugging or automation:

```bash
# See all available commands
uv run hf_model_evaluation/scripts/evaluation_manager.py --help

# Check version
uv run hf_model_evaluation/scripts/evaluation_manager.py --version
```

---

## Understanding the model-index Format

The `model-index` is a YAML structure in the model card's frontmatter that makes evaluation scores machine-readable:

```yaml
---
model-index:
  - name: My-Model-7B
    results:
      - task:
          type: text-generation
        dataset:
          name: Benchmarks
          type: benchmark
        metrics:
          - name: MMLU
            type: mmlu
            value: 85.2
          - name: HumanEval
            type: humaneval
            value: 72.5
        source:
          name: Model README
          url: https://huggingface.co/username/model
---

# My Model 7B
...rest of README...
```

Key fields:
- **`name`**: Display name for the model
- **`task.type`**: What the model does (e.g., `text-generation`, `question-answering`)
- **`metrics`**: Individual benchmark scores with `name`, `type`, and `value`
- **`source`**: Attribution for where the data came from

> [!WARNING]
> The `model-index` schema was defined Papers with Code and is currently under active development. However, a new standard with improved integrations to Hugging Face tools is on the way.

## Troubleshooting

Using skills with coding agents is not deterministic, so let's explore some of the error modes we can expect and how to solve them. 

### "No evaluation tables found"

The README might not have markdown tables, or they might be in a format the parser doesn't recognize.

```
Show me the raw README content so I can see the table format
```

It's best to take a look at the readme and either prompt the agent to parse a specific section, or try another approach. 

### "Could not find model in table"

For tables with multiple models, you may need to specify which one:

```
Use --model-name-override with the exact text "**OLMo 3-32B**"
including the markdown formatting
```

Authors are not consistent with their tables or column naming. 

### "Token does not have write access"

Your HF_TOKEN needs write permissions. Generate a new token at [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens) with "Write" scope enabled.

### "Model not found in Artificial Analysis"

Check the exact URL on artificialanalysis.ai—the creator-slug and model-name come from the URL path.


## Conclusion

Using an AI coding agent with the `hf_model_evaluation` skill makes it easy to contribute structured evaluation metadata to the Hugging Face Hub. Instead of wrestling with YAML syntax and CLI flags, you describe what you want in natural language.

Some next steps are:

- Add eval scores to your own models.
- Run evals on public models using Inference Providers.
- Import scores from third party sources like Artificial Analysis.
- Run evals on Hugging Face Jobs with custom models. 

Happy evaluating!

## Resources

- [SKILL.md](https://github.com/huggingface/skills/blob/main/hf_model_evaluation/SKILL.md) — Full skill documentation
- [Usage Examples](https://github.com/huggingface/skills/blob/main/hf_model_evaluation/examples/USAGE_EXAMPLES.md) — Additional worked examples
- [Model-Index Spec](https://github.com/huggingface/hub-docs/blob/main/docs/hub/model-cards.md) — Official Hugging Face specification
- [inspect-ai](https://github.com/UKGovernmentBEIS/inspect_ai) — Evaluation framework for Method 3
- [Artificial Analysis](https://artificialanalysis.ai) — Third-party benchmark data source
