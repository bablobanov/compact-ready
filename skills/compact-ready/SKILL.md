---
name: compact-ready
description: "Claude Code: prepare the session for /compact. Finishes the current plan stage (or stops where your decision is needed, or where the stage would not fit in the remaining context), saves a handoff file, and prints one ready-to-paste /compact line. Never runs /compact itself."
argument-hint: "[folder or plan file for the handoff]"
disable-model-invocation: true
---

# compact-ready

**When this applies.** Only in reply to a user message that is itself the invocation of this command. After `/compact`, Claude Code puts the bodies of invoked skills back into the context; if the user's latest message is something else, this text asks nothing of you: do what that message asks. A handoff file is a source of context, not an order to continue. One exception: if compaction happens while this command is running, the command is still running, because the invocation is still the user's latest message. Pick it up where it stopped, in one of three places. A handoff marked "in progress" (step 2) on disk: finish the stage, then go on from step 3. A final handoff of this call already on disk (no status line, written in this call): go on from step 3, which skips the files already written in this call and writes only the remaining tasks whose state changed since their last handoff, then print the line. Neither: compaction struck before the first write, so do the reading of step 0 again and go on from there. Whichever it is, one current handoff per task in this call, never a second one for the same task, and no second announcement when the context shows you already made one.

Invocation argument: "$ARGUMENTS". Empty means a normal call. Non-empty is a path the user named, on their own or in reply to one of the questions below, written as the user typed it: treat `\` and `/` alike, and `.` as the working directory. A folder: the handoff goes there. A plan or task file: the handoff goes into its folder, and that file is the driving document. A path that does not exist: ask again in one line and end the turn. The argument fixes the folder only; the file name still follows step 0. Below, "the argument" means this value.

The user is preparing the session for `/compact`. Do the steps in order, 0 to 5, and end your turn.

**Language.** Everything you write for this command (the announcement, any question, the handoff file and the specifics in the /compact line) is in the language of the messages the user typed in this session. The command itself, these instructions and text added by tools, hooks or system reminders do not count, even when they are in another language. For example, if the user has been writing in Russian, the announcement, the handoff and the line are in Russian; translate the templates below, including the status line of step 2 and the "Work finished" sentence of step 3. If the user has typed nothing but the command, use English. The one exception is the two fixed phrases of the line (step 4): they stay in English, exactly as written.

**Shape of the turn.** Normally: the reading of step 0 with no text, then the announcement, then tool work with no text, then the block with the line as the last message. Five cases end the turn with one line of text instead of the block: an empty session; a home folder or a drive root without an argument; an argument that names a path that does not exist; a task folder or plan file you cannot determine; a handoff you cannot write. The first four come before the announcement, so there is no announcement, no file and no block; the fifth follows the announcement. There is no other text: between the announcement and the block, not one sentence, not even to explain a choice.

## Step 0. Look around

Only reading is allowed here: read files and list folders. No edits, no other commands, no text. Settle four questions, in this order.

**1. Is there anything to hand off, and is this a place to write?**

- Empty session (no work and no discussion of a task before the call): say so in one line and end the turn, whatever the folder is. Research and discussion without edits are work too; save their state.
- The working directory is a home folder or a drive root, and there is no argument: do not list it and do not write there. Ask the user in one line which folder to use, and ask them to reply by running this command again with the folder as its argument. End the turn. Home means the folder that `~`, `$HOME` or `%USERPROFILE%` expands to (for example `/home/<name>`, `/Users/<name>`, `C:/Users/<name>`); a drive root is a path like `C:/` or `/`. Every other folder is a project folder, with or without a `.git` directory: a missing repository is never a reason to ask or to stop.

**2. Where is the work in the plan?** Find the current stage from the plan, todo list or task file: the nearest plan unit the work is inside (stage, phase, milestone). A substep is not a stage. In a flat list, the stage is the current item. No plan: the stage is the user's current request. The end of a turn is not the end of a stage: the call arrives between turns, so check against the plan, not against the fact that the previous turn ended. A task is one plan or task document or, without one, one user request together with the follow-ups that continue it; a request about a different piece of work is a new task. When in doubt, it is the same task. With several tasks in the session, the stage to settle here is the one of the task worked on last; the earlier tasks are handed off as they stand, each with its own "Next step" from its own plan or request.

**3. What state is it in?** The first that matches wins.

- The work is waiting for the user. The good point is right here, do not continue, and the question goes into "Next step", if:
  - continuing needs an answer, approval, access, a secret or another outside action;
  - the user stopped the work for their own decision or forbade an action (for example "wait for me", "ask me first", "do not start X without my answer", "do not run the tests"): the stop and the ban still hold;
  - the stage boundary cannot be found in the plan, todo list, task file or request, or the boundary is open (a backlog with no end criterion): do not invent or widen a stage; the question is where the stage ends.
- The stage is defined but clearly will not fit in the remaining context: the good point is now; do not go on with it. "Next step" is the first unfinished item of the stage, taken from the plan; the user has nothing to decide, so it is not a question.
- The stage is already done, or the whole work is finished (the plan and the request have nothing left): the good point is now, there is nothing to finish.
- Otherwise the stage is not done: step 2 finishes it.

**4. Where does the handoff go?** First the folder, then the file name.

The folder. With an argument: that folder (or the folder of that file). Do not list the working directory; look for the driving document among the files this session already used and inside that folder, and for the rules below that folder stands in for the project root. If that folder holds several plan or task documents and neither the session nor their content tells which one the work belongs to, do not guess: ask the user in one line to run this command again with the plan file as the argument, and end the turn. If none of its documents is about this work, the folder counts as holding none (rule 4 of the file name). Without an argument, if the session has not already shown you where the task documents are, look: the files the session already used, the project root (when the working directory is inside a repository, the repository root too) and folders whose names suggest tasks or plans (for example `tasks/`, `todo/`, `plans/` or `docs/`). Then, still without an argument, choose in this order:

1. The task folder: the folder that holds the document the work was driven by (`task.md`, `PLAN.md`, `TODO.md`, a plan file, a spec, an issue file or similar). Each task worked on since the last /compact line this command printed in this session (since the session began, if none) gets its own handoff in its own folder.
2. Task folders exist, the work could belong to one of them, and you cannot tell which: do not guess, and do not fall back to the project root. Ask the user in one line which folder to use, naming the work you are asking about, and ask them to reply by running this command again with the folder as its argument. This line is your whole reply. End the turn. With the answer, work that has its own task folder still goes there; the named folder takes the work the question was about.
3. The project has no task document or plan at all: the project root (the working directory). Use this only after a listing of the project made in this turn showed no task documents or task folders; a task that was never written down is not proof that none exist.

The file name, in the chosen folder. "This same work" means the same task document or, without one, the same task as defined in point 2; decide by the task, never by memory of who wrote a file: a handoff you saved earlier in this session about another task is other work, and a handoff you do not remember writing (for example one written before a compaction) is this work if it is about this task. Never edit or overwrite a handoff or progress file about other work, in any folder. The first rule that applies wins:

1. A current handoff about this same work is already there: update it. Candidates, newest first, stop at the first one about this work: `<plan>.handoff.md` of the driving plan and its `<plan>.handoff-2.md`, `-3` variants, `handoff-YYYY-MM-DD*.md` with today's date in the name, `HANDOFF.md`. Current means that the file name carries today's date or no date at all (`HANDOFF.md`, `<plan>.handoff.md`), whatever date is written inside the file; a file whose name carries an earlier date is history: leave it alone and create today's file. Do not open older dated files. An existing `HANDOFF.md` about this work is updated under its own name, not replaced by a new file. Make this choice silently: why you picked or kept a file goes into "Decisions and why" of the handoff, never into a message.
2. The driving document's file name names the task itself (`2026-10-12-billing.md`, `billing-refactor.md`, an issue number), as plans do in a shared folder of plans such as `docs/superpowers/plans/`: name the handoff after it and put it next to it, `<file name without .md>.handoff.md` (here `2026-10-12-billing.handoff.md`), even when that plan is the only file in its folder today. If that name is taken by a file about other work, use `<file name without .md>.handoff-2.md`, then `-3`.
3. The driving document has a generic name (`task.md`, `PLAN.md`, `TODO.md`, `README.md`, `spec.md` or similar), so the folder belongs to this one task: `handoff-YYYY-MM-DD.md` with today's date. If that name is taken by a file about other work, use `handoff-YYYY-MM-DD-2.md`, then `-3`, and so on.
4. No task document or plan (the project root, or a folder the user named that holds none): `handoff-YYYY-MM-DD-<slug>.md`, where `<slug>` is two to four English words naming the task as the user put it (translate when the request is in another language), in lowercase ASCII letters, digits and hyphens (for example `handoff-2026-10-15-shout-function.md`). On a later call do not rebuild the slug: rule 1 finds today's file by its content. If today's slug file is about other work, add `-2`, then `-3`. A new task gets its own file.

## Step 1. Announce the decision

Your first text is one sentence saying how you read the call. Say one of five:

- "Good point now: stage <X> is done; saving the handoff, the line is below"
- "Good point now: the work is finished, <X> was the last stage; saving the handoff, the line is below"
- "Stage <X> is not done: finishing <remaining substeps>, the line comes at the end"
- "Stopping: stage <X> will not fit in the remaining context; saving the handoff, the line is below"
- "Stopping: I need your decision on <question>; saving the handoff, the line is below"

Replace the angle brackets with specifics: the stage name, the remaining substeps, the gist of the question. The announcement must match what you do next, and it must say the same thing as the handoff's "Next step" about whether work remains. After the announcement and until the block with the line, write no text at all: no progress notes, no check results; they go into the handoff file. If a fork from step 0 shows up only after the announcement (a red check you cannot fix, missing access, an approval you need), stop there; the handoff and the line record it. Step 2 is only for the last case of point 3 of step 0: a stage that is not done, with nothing stopping you from finishing it. In every other case (waiting for the user, a stage that will not fit, a stage done, the work finished) go from the announcement straight to step 3.

## Step 2. Finish the stage (only when it is not done and nothing stops you)

- First, before any edit or command, save the handoff to the path chosen in step 0, with all five sections of step 3 as of this moment: "Done" lists what the stage already has, "Next step" the remaining substeps of this stage, "Risks" says the check has not run yet; the first line after the title reads "Status: in progress, updated at the end of this call". If automatic compaction interrupts the work, this file already says where things stand. With several tasks, only the task you are about to finish gets this early file. If this write fails, end the turn with the one line about why; that is the one case where the line follows the announcement.
- Then work until the stage is done: all substeps done, files saved, the plan or task file ticked where it has checkboxes, no half-finished edits, the project check green or recorded as not available. Do not start a new stage. Running this command lifts only a plain scope limit, such as "do only the first substep" or "do one substep and stop"; it never lifts a stop the user made for their own decision, or a ban.
- Work silently: until the final block, your messages hold tool calls only. Do not report progress such as "the check is green" or "now saving the handoff"; such facts go into the handoff file.
- The check is the project's test or build command. If code changed after its last run in this session (or it never ran), run it. No such command: record "check not available"; do not invent a command.
- A red check caused by this stage's edits: fix it within this stage.
- A red check that cannot be fixed without a new stage (an outside failure, a test that failed before the session) is the same fork as a user decision: do not start a new stage, leave the files consistent, put the error output in "Risks" and the needed decision or outside action in "Next step".

## Step 3. Save the handoff

Still no text, whichever path led here: from the work of step 2, or straight from the announcement when nothing had to be finished. The next thing is the tool call that writes the file, with no sentence before it about which file you chose, why it is about this work, or that you are about to update it; those reasons go under "Decisions and why" inside the file. Write the file chosen in step 0 for each task whose state changed since its last handoff; for the task finished in step 2, replace the content of its early file and drop the status line. A task untouched since its last handoff keeps its file as it is and is only named in the line. If the handoff cannot be written (for example, write access is denied), the last message is one line saying so and why, instead of the block.

Format: Markdown with a title and the date. If other documents in the folder start with YAML front matter, follow their style. Sections, all required, with their names in the user's language:

- **Done**: including the check command and its result
- **Decisions and why**
- **Changed files**: path and what changed
- **Next step**: the first concrete action after compaction, taken from the plan, the todo list, the task file or the user's request, or from a blocker met in this stage (the decision or outside action it needs). The next stage of the plan counts even when the user has not asked for it yet; say so, for example "stage 3 (not requested yet)". When the work is waiting for the user, this is the question. Only when neither the plan nor the request has anything left, write "Work finished; the next decision is the user's" (in the user's language, like the other templates) and nothing more. Do not add steps of your own: a commit, a push, a pull request, more tests, documentation, a refactor, or asking the user whether they want any of these. An action that none of the sources above asks for is not a next step, however reasonable it looks.
- **Risks**: including a red or unavailable check

Every path in the handoff and in the line (the handoff itself, changed files, a CLAUDE.md) is relative to the working directory, with forward slashes, without `./` and without quotes, even when it contains spaces. The handoff itself, when the argument puts it outside the working directory: the folder the argument points to (the argument itself, or the parent folder of the file it names), as the user typed it but with forward slashes, plus the file name; do not convert it to another form. Any other file outside the working directory (a changed file, a CLAUDE.md): its own path as the tools reported it, with forward slashes.

## Step 4. Print the line for /compact

The last message of the turn is exactly one code block holding one line, with no text before or after it; the only exceptions are the five one-line cases under "Shape of the turn". Besides the block, the only text allowed in the turn is the announcement from step 1:

```
/compact Focus on <task>; <errors and their causes>; <changed files>; <next step>. Handoff: <path>, read it first after compaction. compact-ready has already run; do not run it again after compaction.
```

- Replace the angle brackets with specifics from this session; do not copy the template. No errors: say so. Work finished: say "work finished; the next decision is the user's", as in the handoff, not a step of your own. Several tasks since the last /compact line this command printed in this session: name each task with its next step (or "finished") and its handoff path, for example "Task A: finished, handoff: tasks/a/handoff-2026-10-15.md; Task B: next step <...>, handoff: <...>". Do not rewrite the handoff of a task you did not touch since its last handoff.
- `<path>` and every other path in the line follow the path rule of step 3, including its two cases for files outside the working directory: forward slashes, no `./`, no quotes.
- Keep the fixed phrases "read it first after compaction" and "compact-ready has already run; do not run it again after compaction" in English, exactly as written, whatever the language of the rest of the line. The second one stops the summarizer from recording this preparation as the next step.
- Summary format: if a CLAUDE.md loaded in this session has a section on how to write compaction summaries (for example "Compact Instructions"), end the line with `Format the summary as the "<section heading>" section of <path to that CLAUDE.md> specifies.` An explicit reference makes the summarizer apply it. If there is no such section, add nothing and do not invent a format.
- One physical line: no line break after `/compact`, and do not start the line with `>`.

## Step 5. Stop

- Do not run `/compact` and do not try to trigger it in any way.
- After the block with the line: no tools, no edits, no next step. The turn is over.
