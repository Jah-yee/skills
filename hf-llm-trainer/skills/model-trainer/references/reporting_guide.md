# Reporting Guide

How to report ML experiments and results.

Create a directory called `training_reports` in the root of the project.

Create a markdown file for each experiment.

The file should be named `experiment_name.md`.

The file should contain each run from the experiment and each run should have the following sections:

- Base Model & Dataset
- Run Title [contains run name, timestamp, status]
- Training Parameters
- Run Status
- Run Job link
- Trackio logs
- Run Evaluations

## Base Model & Dataset

The base model and dataset are the base model and dataset used for a group of runs.

For every new base model or dataset, create a new section for runs using that base model or dataset.

EXAMPLE:

```md
# Base Model & Dataset

[Base Model](https://huggingface.co/Qwen/Qwen2.5-0.5B)
[Dataset](https://huggingface.co/datasets/trl-lib/Capybara)
```

## Run Title

Generate a title for the run based on the run name and timestamp.

EXAMPLE:

```md
# `run-0000000000` - `2025-01-01 00:00:00` - `Success`
```

### Run Status

One of the following:

- Success
- Failure
- In Progress
- Completed

REMINDER: Update the status of the run when the run is completed.

## Training Parameters

The training parameters are the training parameters used for the run. Use a markdown table to list the training parameters.

```md
| Parameter | Value |
|-----------|-------|
| Learning Rate | 2e-5 |
| Batch Size | 32 |
```

## Run Logs

Link to the logs of the job on hugging face jobs or the local process id.

EXAMPLE:

```md
# Run Logs

[Run Logs](https://huggingface.co/jobs/burtenshaw/qwen-capybara-sft/runs/1761394091)
```

## Trackio logs

- Link to the trackio logs of the run.
- iframe of the trackio logs embedded in the report.

EXAMPLE:

```md
# Trackio Logs

[Trackio Logs](https://burtenshaw-trackio.hf.space?project=huggingface&runs=burtenshaw-1761394091,burtenshaw-1761396497,burtenshaw-1761397045,burtenshaw-1761400602,burtenshaw-1762803812,burtenshaw-1762940024&sidebar=hidden&navbar=hidden)

<iframe src="https://burtenshaw-trackio.hf.space?project=huggingface&runs=burtenshaw-1761394091,burtenshaw-1761396497,burtenshaw-1761397045,burtenshaw-1761400602,burtenshaw-1762803812,burtenshaw-1762940024&sidebar=hidden&navbar=hidden" style="width:1600px; height:500px; border:0;"></iframe>
```

## Experiment Evaluations

The experiment evaluations are the evaluations of the entire experiment. Use a markdown table to list the evaluations.

EXAMPLE:

```md
# Experiment Evaluations

| Run Title | Benchmark | Score | Evaluation Job Link | Model Link |
|-----------|-----------|-------|--------------------|--------------------|
| `run-0000000001` - `2025-01-01 00:00:00` - `Success` | MMLU | 85.2 | [Evaluation Job Link](https://huggingface.co/jobs/burtenshaw/qwen-capybara-sft/runs/1761394091) | [Model Link](https://huggingface.co/burtenshaw/qwen-capybara-sft) |
```