---
name: codex
description: Use when the user asks to run Codex CLI (codex exec / codex resume) or references Codex for code analysis, refactoring, or automated editing with gpt-5.1 / gpt-5.1-codex models.
---

# Codex Skill Guide (v2)

## 1. When to use this skill

Trigger this skill whenever the user:

* Explicitly asks to run `codex`, "Codex CLI", or `codex exec` / `codex resume`.
* Asks for non-trivial code analysis, refactoring, migration, or large-scale automated editing that is better handled by the Codex CLI than by the assistant alone.
* References existing Codex work and wants to "resume" or "continue" a previous Codex session.

The skill assumes the user has a shell alias / wrapper similar to:

```bash
codex -m gpt-5.1-codex-mini --yolo -c model_reasoning_effort="high" --search "$@" resume
```

You are responsible for choosing:

* The model (`-m`).
* The reasoning effort (`model_reasoning_effort`).
* Whether to keep `--yolo` or not.
* Sandbox level (if applicable).
* Whether this is a new exec or a resume.

---

## 2. Model selection

Pick the model based on the task, in this priority order:

1. **`gpt-5.1-codex-mini` (default)**

   * Use for:

     * Quick refactors.
     * Localized edits in a small/medium codebase.
     * Fast iterations and trying multiple variations.
   * Default unless the user explicitly needs deeper reasoning or higher fidelity.

2. **`gpt-5.1-codex`**

   * Use for:

     * Large-scale refactors spanning many files.
     * Complex framework migrations (e.g., Combine → async/await, networking layer redesign, etc.).
     * Non-trivial codebase restructuring where deep code understanding is required.

3. **`gpt-5.1`**

   * Use for:

     * Mixed tasks that are half product/architecture design and half code.
     * When the focus is on reasoning, trade-offs, documentation, API design, not just code edits.
     * Explaining architecture or generating specs that Codex then implements.

4. **`gpt-5`**

   * Use for:

     * Very high-level design, trade-off analysis, or long-form documentation that will later be implemented in code.
     * Cases where the user explicitly asks for the strongest general model.

### Model decision rules

* If the user does not specify a model:

  * Default to `gpt-5.1-codex-mini`.
* If the user explicitly asks for:

  * "deeper refactor / understand the whole project" → use `gpt-5.1-codex`.
  * "design the architecture / API / spec first, then implement" → use `gpt-5.1`.
  * "use gpt-5 for this Codex run" → use `gpt-5`.
* If the user mentions latency or cost concerns → prefer `gpt-5.1-codex-mini`.

When you present a plan to the user or summarize, always state clearly:

* Which model you intend to use.
* Why it fits this task.

---

## 3. Reasoning effort (`model_reasoning_effort`)

You control the configuration via:

```bash
-c model_reasoning_effort="<high|medium|low>"
```

Guidelines:

* **`high` (default)**

  * Use for substantial refactors, migrations, or anything risky to the codebase.
  * Default when using `gpt-5.1-codex-mini` or `gpt-5.1-codex` unless the user explicitly requests otherwise.

* **`medium`**

  * Use for:

    * Routine edits where the scope is clearly bounded.
    * Repetitive clean-ups, adding logs, mechanical pattern replacements with some understanding.

* **`low`**

  * Use for:

    * Very small changes (e.g., rename a single symbol, adjust one function).
    * Bulk mechanical changes that are already very well-specified by the user.

If the user explicitly specifies a reasoning effort, always respect it.

---

## 4. Safety / automation level: `--yolo` and sandbox

### `--yolo`

`--yolo` means Codex can act more aggressively and automatically:

* Default: **enable `--yolo`** for:

  * The standard development workflow where Codex is already trusted.
  * Tasks where the user wants "fully automatic handling" and is comfortable reviewing diffs afterwards.

* Consider omitting `--yolo` when:

  * The user wants to step through changes cautiously.
  * The task is very sensitive (e.g., release branches, production infra scripts) and the user explicitly asks for conservative behavior.

If you decide to omit `--yolo`, explicitly note this in your summary to the user.

### Sandbox mode

If your Codex setup supports `--sandbox`, use:

* `--sandbox read-only`

  * For analysis/audit only, no edits.
  * Use when the user wants to "see the report first" or pure review.

* `--sandbox workspace-write`

  * For applying local edits in the existing workspace.
  * Use for normal development refactors and code transformations.

* `--sandbox danger-full-access`

  * For tasks requiring broader system or network access.
  * Only use when the user has explicitly requested such a capability and understands the risk.

When using `workspace-write` or `danger-full-access` together with `--yolo`, treat it as high-impact and make this clear in your explanation.

---

## 5. Command assembly

Always include:

* `-m <MODEL>`       (see Section 2)
* `-c model_reasoning_effort="<high|medium|low>"`
* `--skip-git-repo-check`
* Optional: `--sandbox <read-only|workspace-write|danger-full-access>`
* Optional: `--yolo` (see Section 4)
* Optional: `-C <DIR>` if the task should run from a different project root.
* Optional: `--search "<query>"` when using the user's wrapper style.

### 5.1 New task template

For a new task, the canonical command pattern is:

```bash
codex exec \
  -m <MODEL> \
  --skip-git-repo-check \
  -c model_reasoning_effort="<high|medium|low>" \
  [--sandbox read-only|workspace-write|danger-full-access] \
  [--yolo] \
  [--search "<query>"] \
  2>/dev/null
```

If you are following the user's alias convention, you can conceptually think of it as:

```bash
codex -m <MODEL> \
  --yolo \
  -c model_reasoning_effort="<high|medium|low>" \
  --search "$@" \
  resume \
  2>/dev/null
```

but when you explain or emit commands, keep the structure explicit and readable.

### 5.2 Resuming a previous session

When continuing a previous Codex session:

```bash
echo "<your new prompt here>" | codex exec --skip-git-repo-check resume --last 2>/dev/null
```

Rules:

* Do **not** change model, reasoning effort, or sandbox when resuming, unless the user explicitly asks to.
* Do **not** add extra configuration flags around `resume` by default.
* You may only modify:

  * The piped prompt text.
  * Whether you are resuming the very last session or a specific previous one (if the CLI supports that and the user has requested it).

---

## 6. Output handling and user feedback

1. Run the `codex` command.
2. Capture stdout; stderr should be suppressed by default with `2>/dev/null`.

   * Only surface stderr when:

     * The user explicitly wants to see thinking tokens or detailed logs.
     * You need to debug an error and the user has agreed.
3. Summarize for the user:

   * Which model was used.
   * Reasoning effort level.
   * Whether `--yolo` and which sandbox mode were used.
   * What Codex did:

     * Files it touched or analyzed.
     * Main transformations or decisions.

After Codex completes, inform the user that they can resume:

> You can resume this Codex session at any time by saying "codex resume" or by asking to continue with additional analysis or changes.

(Adapt the wording to the user's language and context.)

---

## 7. Follow-up and iteration

After every `codex` run:

1. Briefly propose clear next steps, for example:

   * "Do you want Codex to run additional refactors on module X?"
   * "Should we now apply the same pattern to target Y?"
   * "Do you want to switch to `gpt-5.1-codex` for a deeper, cross-module refactor?"

2. When the user wants to continue with Codex:

   * Prefer `resume --last` so Codex keeps context of the previous run.
   * Pipe a focused follow-up prompt via stdin:

```bash
echo "<new instructions / constraints>" | codex exec --skip-git-repo-check resume --last 2>/dev/null
```

3. If the user wants to change model or reasoning effort mid-stream:

   * Start a **new** `codex exec` with the updated `-m` and `model_reasoning_effort`.
   * Explain that you are starting a fresh Codex run with the new configuration.

---

## 8. Error handling

* If `codex --version` or any `codex exec` command exits non-zero:

  * Stop.
  * Report the failure in plain terms (include any relevant stderr if helpful).
  * Ask the user how they want to proceed (e.g., retry, adjust model or flags, or manually inspect).

* For partial results or warnings:

  * Summarize:

    * What Codex managed to complete.
    * What was skipped or failed.
  * Propose concrete options:

    * Rerun with a more powerful model or higher reasoning effort.
    * Narrow the scope.
    * Adjust automation level (`--yolo` vs. more conservative run).

Always err on the side of clarity: make it obvious which Codex configuration you chose and why, and how the user can repeat or adjust that configuration in future runs.
