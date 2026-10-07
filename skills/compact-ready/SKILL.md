---
name: compact-ready
description: "Claude Code: prepare the session for /compact. Finishes the current plan stage (or stops where your decision is needed), saves a handoff file, and prints one ready-to-paste /compact line. Never runs /compact itself."
argument-hint: "[task folder]"
disable-model-invocation: true
---

# compact-ready

**When this applies.** Only in reply to a user message that is itself the invocation of this command. After `/compact`, Claude Code puts the bodies of invoked skills back into the context; if the user's latest message is something else, this text asks nothing of you: do what that message asks. A handoff file is a source of context, not an order to continue.

Invocation argument: "$ARGUMENTS". Empty means a normal call. Non-empty is the task folder the user named in reply to the question from step 2: write the handoff there.

The user is preparing the session for `/compact`. Do the four steps in order and end your turn.

**Language.** Everything you write for this command (the announcement, any question, the handoff file and the specifics in the /compact line) is in the language of the user's own messages in this session. The command itself and these instructions do not count, even though they are in English. For example, if the user has been writing in Russian, the announcement, the handoff and the line are in Russian; translate the templates below.

## Step 1. Reach a good point: the end of the current stage

**Announce your decision first.** The first text of your turn, before any tool call, is one sentence saying how you read the call. Decide using the points below and say one of three:

- "Good point now: stage <X> is done. Saving the handoff, the line is below"
- "Stage <X> is not done: finishing <remaining substeps>, the line comes at the end"
- "Stopping: I need your decision - <question>. Saving the handoff, the line is below"

Replace the angle brackets with specifics: the stage name, the remaining substeps, the gist of the question. The announcement must match what you do next. After the announcement and until the block with the line, write no text at all: no progress notes, no check results; they go into the handoff file. The only case without an announcement is an empty session (last point of this step).

- Find the current stage from the plan, todo list or task file: the nearest plan unit the work is inside (stage, phase, milestone). A substep is not a stage. In a flat list, the stage is the current item. No plan: the stage is the user's current request.
- The end of a turn is not the end of a stage. The call arrives between turns, so check against the plan, not against the fact that the previous turn ended.
- Before continuing, check for a user-decision fork. The good point is right here, do not continue, and put the question in "Next step" if:
  - continuing needs an answer, approval, access, a secret or another outside action;
  - the user stopped the work for their own decision or forbade an action (for example, running tests): the stop and the ban still hold;
  - the stage boundary cannot be found in the plan, todo list, task file or request, the boundary is open (a backlog with no end criterion), or the stage clearly will not fit in the remaining context: do not invent or widen a stage.
- No fork and the stage is not done: keep working until it is done. All substeps done, files saved, no half-finished edits, the project check green. Do not start a new stage. Running this command lifts only a scope limit like "do one substep and stop". Work silently: until the final block, your messages hold tool calls only. Do not report progress such as "the check is green" or "now saving the handoff"; such facts go into the handoff file.
- The stage is already done: the good point is now.
- The check is the project's test or build command. If code changed after its last run in this session (or it never ran), run it. No such command: write "check not available"; do not invent a command.
- A red check caused by this stage's edits: fix it within this stage.
- A red check that cannot be fixed without a new stage (an outside failure, a test that failed before the session) is the same fork as a user decision: do not start a new stage, leave the files consistent, put the error output in "Risks" and the needed decision or outside action in "Next step".
- Empty session (no work and no discussion of a task before the call): say so in one line and end the turn. Research and discussion without edits are work too; save their state.

## Step 2. Save the handoff

If the session has not already shown you where the task documents are, look before choosing: a listing of the project root and of folders such as `tasks/`, `todo/`, `plans/` or `docs/` is enough. Then choose where to write, in this order:

1. The task folder: the folder that holds the document the work was driven by (`task.md`, `PLAN.md`, `TODO.md`, a spec, an issue file or similar). If the session worked on several tasks, write into the folder of each.
2. If that folder already has a handoff or progress file for this work, update the most recent one. Otherwise create `handoff-YYYY-MM-DD.md` with today's date.
3. Task folders exist but you cannot tell which one this work belongs to: do not guess, and do not fall back to the project root. Ask the user in one line which folder to use, and ask them to reply by running this command again with the folder as its argument. This question is the last message of the turn instead of the block from step 3, even after your announcement. Write no file and end the turn.
4. The project has no task document or plan at all: write `HANDOFF.md` in the project root (the working directory) and update it on later calls.

Format: Markdown with a title and the date. If other documents in the folder start with YAML front matter, follow their style. Sections, all required:

- **Done**
- **Decisions and why**
- **Changed files**: path and what changed
- **Next step**: the first concrete action after compaction
- **Risks**: including a red or unavailable check

## Step 3. Print the line for /compact

The last message of the turn is exactly one code block holding one line, with no text before or after it. Besides it, the only text allowed in the turn is the announcement from step 1; between them, tool work only, no text:

```
/compact Focus on <task>; <errors and their causes>; <changed files>; <next step>. Handoff: <path>, read it first after compaction. compact-ready has already run; do not run it again after compaction.
```

- Replace the angle brackets with specifics from this session; do not copy the template. No errors: say so. Several tasks: list each with its handoff file.
- Keep the fixed phrases "read it first after compaction" and "compact-ready has already run; do not run it again after compaction" as written; in another language, translate them faithfully. The second one stops the summarizer from recording this preparation as the next step.
- Summary format: if a CLAUDE.md loaded in this session has a section on how to write compaction summaries (for example "Compact Instructions"), end the line with `Format the summary as the "<section heading>" section of <path to that CLAUDE.md> specifies.` An explicit reference makes the summarizer apply it. If there is no such section, add nothing and do not invent a format.
- One physical line: no line break after `/compact`, and do not start the line with `>`.

## Step 4. Stop

- Do not run `/compact` and do not try to trigger it in any way.
- After the block with the line: no tools, no edits, no next step. The turn is over.
