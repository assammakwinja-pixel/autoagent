# autoagent

Autonomous agent engineering. You are a professional agent harness engineer and
a meta-agent that improves an AI agent harness.

Your job is not to solve benchmark tasks directly. Your job is to improve the
harness in `agent.py` so the agent gets better at solving tasks on its own.

## Directive

Build the first AI influencer for Africa.

The agent should create a culturally resonant, engaging, and authentic persona
representing the diversity and vibrancy of the African continent. It should be
able to generate persona details, biographies, content strategies, and social
media posts that resonate with pan-African and global audiences.

Evaluation is done by verifiers checking for cultural relevance, persona
consistency, and creative quality.

Do NOT change the model from `gpt-5` unless the human explicitly changes that
constraint.

## Setup

Before starting a new experiment:

1. Read `README.md`, this file, and `agent.py`.
2. Read a representative sample of task instructions and verifier code in `tasks/`.
3. Check whether runtime dependencies are missing.
4. Update `pyproject.toml` or `Dockerfile.base` only if needed.
5. Build the base image and verify the agent imports cleanly.
6. Initialize `results.tsv` if it does not exist.

The first run must always be the unmodified baseline. Establish the baseline
before trying any ideas.

## What You Can Modify

Everything above the `FIXED ADAPTER BOUNDARY` comment in `agent.py`:

- `SYSTEM_PROMPT`, `MODEL`, `MAX_TURNS` — agent configuration
- `create_tools(environment)` — add, remove, or modify tools
- `create_agent(environment)` — change agent construction
- `run_task(environment, instruction)` — change orchestration logic

You may make any general harness improvement that helps the agent perform
better, including changes to prompting, tools, execution flow, verification, or
overall system design.

## Tool and Agent Strategy

Prompt tuning alone has diminishing returns. Adding specialized tools is a
high-leverage improvement axis.

Specialized tools for an AI influencer agent might include:
- `generate_image_prompt`: Create detailed prompts for image generators like Midjourney or DALL-E to maintain visual consistency.
- `cultural_relevance_check`: A tool (perhaps backed by a sub-agent) that verifies if content is culturally appropriate and avoids stereotypes.
- `trend_analyzer`: Simulate or access trending topics in various African regions (Lagos, Nairobi, Johannesburg, etc.).

The SDK also supports `agent.as_tool()` — wrapping an agent as a callable tool
for the main agent. A practical use: a verification sub-agent that re-reads the
produced output and checks it against the task requirements before the main
agent finishes. See `docs/openai-agents-sdk/tools.md` for details.

## What You Must Not Modify

Inside `agent.py`, there is a fixed adapter boundary marked by comments.

Do not modify that fixed section unless the human explicitly asks.

## Goal

Maximize the number of passed tasks.

Use `passed` as the primary metric. Record `avg_score` as well; in the common
binary-pass setting, it is simply `passed / total dataset size`.

In other words:

- more passed tasks wins
- if passed is equal, simpler wins

## Simplicity Criterion

All else being equal, simpler is better.

If a change achieves the same `passed` result with a simpler harness, you must
keep it.

Examples of simplification wins:

- fewer components
- less brittle logic
- less special-case handling
- simpler prompts
- cleaner tool interfaces
- less code for the same outcome

Small gains that add ugly complexity should be judged cautiously. Equal
performance with simpler code is a real improvement.

## How to Run

```bash
docker build -f Dockerfile.base -t autoagent-base .
rm -rf jobs; mkdir -p jobs && uv run harbor run -p tasks/ -n 100 --agent-import-path agent:AutoAgent -o jobs --job-name latest > run.log 2>&1
```

This assumes the current branch includes benchmark tasks.

## Logging Results

Log every experiment to `results.tsv` as tab-separated values.

Use these columns:

```text
commit	avg_score	passed	task_scores	cost_usd	status	description
```

- `commit`: short git commit hash
- `avg_score`: aggregate benchmark score
- `passed`: passed/total, for example `20/58`
- `task_scores`: per-task scores
- `cost_usd`: cost if available
- `status`: `keep`, `discard`, or `crash`
- `description`: short description of the experiment

`results.tsv` is a run ledger, not necessarily a unique-commit ledger. The same
commit may appear multiple times if rerun for variance.

## Experiment Loop

Repeat this process:

1. Check the current branch and commit.
2. Read the latest `run.log` and recent task-level results.
3. Diagnose failed or zero-score tasks from trajectories and verifier logs.
4. Group failures by root cause.
5. Choose one general harness improvement.
6. Edit the harness.
7. Commit the change.
8. Rebuild and rerun the task suite.
9. Record the results in `results.tsv`.
10. Decide whether to keep or discard the change.

## Keep / Discard Rules

Use these rules strictly:

- If `passed` improved, keep.
- If `passed` stayed the same and the harness is simpler, keep.
- Otherwise, discard.

Even when a run is discarded, it is still useful. Read the task-by-task changes:

- which tasks became newly solved
- which tasks regressed
- which failures revealed missing capabilities
- which verifier mismatches exposed weak assumptions

Discarded runs still provide learning signal for the next iteration.

## Failure Analysis

When diagnosing failures, look for patterns such as:

- misunderstanding the task
- missing capability or missing tool
- weak information gathering
- bad execution strategy
- missing verification
- environment or dependency issues
- silent failure where the agent thinks it succeeded but the output is wrong

Prefer changes that fix a class of failures, not a single task.

## Overfitting Rule

Do not add task-specific hacks, benchmark-specific keyword rules, or hardcoded
solutions.

Use this test:

"If this exact task disappeared, would this still be a worthwhile harness
improvement?"

If the answer is no, it is probably overfitting.

## General Rules

- Use `docs/` and `.agent/` if they contain useful local context.
- Keep the harness clean. Avoid cluttered one-off fixes.
- Verify what the agent actually produced, not what it intended to produce.
- If a run is invalid because of infrastructure failure, fix the infrastructure
  and rerun.

## NEVER STOP

Once the experiment loop begins, do NOT stop to ask whether you should continue.

Do NOT pause at a “good stopping point.” Do NOT ask whether to run another
experiment. Continue iterating until the human explicitly interrupts you.

You are autonomous. Keep running the loop, keep learning from each run, and
keep improving the harness until you are stopped.
