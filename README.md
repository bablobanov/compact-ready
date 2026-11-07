# Compact Ready

![Compact Ready: finish the stage, save the handoff, compact with focus](https://raw.githubusercontent.com/bablobanov/bablobanov/main/assets/compact-ready.jpg)

Listed in the [Claude plugin directory](https://claude.ai/customize/plugins/id/1eb4a2b3-703e-435e-8079-1134b644582d%40anthropic-plugin-directory)

Compact Ready is a plugin for the moment before compaction, for Claude Code, Cowork and the claude.ai chat. It finishes the current stage of your plan, or stops where it needs your decision or where the stage would not fit in the remaining context. Then it writes a handoff file next to your task and prints what comes next: in Claude Code one ready-to-paste `/compact` line with a focus, in Cowork how to continue in a new task, in a chat how to continue in a new chat. It never compacts on its own: in Claude Code you paste the line; in Cowork and in a chat you take the handoff with you.

## Why

Automatic compaction starts near the context limit and writes a general summary. A `/compact` line with a clear focus gives a cleaner continuation, but you have to remember to write it. And when you remember, the work is often half done. Compact Ready closes the current piece of work first, saves the state to a file, and writes the focus line for you.

## Where it works

| Surface | What it does |
|---|---|
| Claude Code (command line, desktop app, editors) | finishes the stage, writes the handoff, prints the `/compact` line |
| Cowork | finishes the stage, writes the handoff into the folder connected to the task, prints how to continue in a new task |
| Chat on claude.ai (web, desktop, mobile) | finishes the stage and hands off for a new chat; there is no `/compact` to run there |

When the skill cannot tell where it runs, it writes the handoff and prints one neutral line with the next step for each surface instead of guessing.

Optional add-on: [compact-ready-context](https://github.com/bablobanov/compact-ready-context), a separate plugin with a small mod, adds the context figures to the call and, in Cowork, the word `context-compact` that compacts with the handoff's focus. The skill works in full without it.

## What happens when you run it

1. **It looks around first.** Before it says anything, it reads your plan, todo list and task files to find the current stage, whether the work is waiting on you, and the folder for the handoff file.
2. **It says how it read the call.** Normally the first sentence is one of five: the stage is done; the whole work is finished; the stage is not done and it will finish it; the stage will not fit in the remaining context; or it needs your decision first. If it cannot tell which task folder or plan file to use, or the working directory is your home folder or the root of a drive, it skips this and only asks which one to use.
3. **It finishes the current stage or stops at a question.** It works until the current stage of your plan is done and the project's test or build command passes, or it notes that the project has none. It does not start the next stage. If going on needs your answer, approval, access or another outside action, or a failing check cannot be fixed within this stage, it stops there and records why. The same when the stage clearly would not fit in the remaining context; "Next step" is then the first unfinished item of the stage. When it is going to finish an unfinished stage, it saves the handoff marked "in progress" before it starts and updates it at the end, so an automatic compaction in the middle still finds the state on disk.
4. **It writes a handoff file.** Sections: Done, Decisions and why, Changed files, Next step, Risks. The file is named after the task: `handoff-YYYY-MM-DD.md` in a task's folder; `<plan name>.handoff.md` next to a plan whose file name names the task, as in `docs/superpowers/plans/`; `handoff-YYYY-MM-DD-<task slug>.md` in the project root (the folder the session runs in) when the project has no task documents. A later call about the same task on the same day updates the same file; a new task gets its own. It never edits a handoff about other work. "Next step" is the first action from your plan or request; when the work is finished, it says so instead of inventing one.
5. **It prints one block and stops.** In Claude Code the last message is a single code block with a `/compact Focus on ...` line: copy it, paste it, press Enter. In Cowork the block names the handoff file and how to continue in a new task. In a chat on claude.ai the block names the handoff and how to continue in a new chat. Paths inside your project folder (the folder the session runs in) are relative to it, and a handoff in a folder you named outside it keeps the form you typed, with forward slashes; in Cowork and in a chat, paths are as the tools report them.

In an empty session, with no work before the call, it says so in one line and does nothing else.

## Install

From the Claude plugin directory: open the [listing](https://claude.ai/customize/plugins/id/1eb4a2b3-703e-435e-8079-1134b644582d%40anthropic-plugin-directory) and click **Add**. Claude Code loads it in sessions where you are signed in with the same claude.ai account. Cowork loads it too, after the desktop app is quit and opened again: in our test a new task alone did not pick up a plugin just added to the account.

From this repository, in Claude Code:

```
/plugin marketplace add bablobanov/compact-ready
/plugin install compact-ready@bablobanov
```

The short `owner/repo` form clones over SSH when your SSH key works with GitHub, and over HTTPS otherwise. To use HTTPS explicitly:

```
/plugin marketplace add https://github.com/bablobanov/compact-ready.git
```

Setting the environment variable `CLAUDE_CODE_PLUGIN_PREFER_HTTPS=1` makes the short form always use HTTPS.

Plugins from third-party marketplaces do not update on their own by default. To get a new version, run `/plugin marketplace update bablobanov`, then run `/reload-plugins`.

## Usage

Run it when you want to compact:

```
/compact-ready
```

Type the command, pick it from the command list, or in Cowork open it from the **+** menu under Plugins. Asking Claude in words to run it, such as "run compact-ready", is refused by design: the command is user-only.

If it cannot tell which task folder the work belongs to, it asks. Answer by running it again with the folder:

```
/compact-ready tasks/billing-refactor
```

The argument can also be a plan file; the handoff then goes next to it. If you start the command in your home folder or at the root of a drive, it asks for a folder the same way and writes nothing until you name one:

```
/compact-ready notes
```

Paste the line it printed. After compaction, tell Claude to go on:

```
continue
```

The handoff file is a working file. Commit it with your task, or add `handoff-*.md` and `*.handoff.md` to `.gitignore`, whichever suits your project.

In the terminal's command list the skill appears as `/compact-ready:compact-ready`; the short `/compact-ready` works when no other command uses that name.

If the command is not found after you install the plugin, run `/reload-plugins` or start a new session, and check in `/plugin` that the plugin is enabled. If something seems lost after compaction, open the handoff file: it records what was done, the decisions, the changed files, the next step and the risks. Check details against your files and task documents. Report problems in the repository's [Issues](https://github.com/bablobanov/compact-ready/issues). For security problems, see [SECURITY.md](SECURITY.md).

## In Cowork

Cowork has no `/compact` command. Run `/compact-ready` from the **+** menu under Plugins; its last block names the handoff and tells you how to continue in a new task: write there `read <path> and continue`. Handoff files go to the folder connected to the task, never into the session's container; with no connected folder the skill says so and writes nothing. With the add-on named above, the block tells you to send `context-compact` instead.

## What it does not do

- It never compacts on its own: compaction starts only from the line you paste.
- It adds no hooks and runs no scripts of its own.
- It has no network code of its own and sends nothing by itself.
- Claude cannot start it on its own. The skill sets `disable-model-invocation: true`, so only you can run it.
- It does not start the next stage of your plan.
- It does not invent a next step. When the work is finished, the handoff and the line say so.

## Privacy

Compact Ready is a single instruction file with no code of its own, and it sends nothing by itself. While it runs, Claude reads your plan, todo list and task files as part of the normal session. To finish the stage it may edit project files and run your project's tests, under your permission settings. Then it writes a Markdown handoff file in your project, in the folder connected to the task, or in a folder you named as the argument. See [PRIVACY.md](PRIVACY.md).

## Limitations

- Tested with Claude Code 2.1.318 in the command line, on Claude Sonnet and Claude Opus models; in Cowork (the Claude desktop app 4.23118.0 on Windows) with a connected folder and without one; and in the chat on claude.ai in the browser. Other versions were not tested.
- In a chat on claude.ai there is no `/compact`: the skill hands off for a new chat, and the chat summarises on its own when it needs to.
- In Cowork there is no `/compact` either: the skill writes the handoff and tells you how to continue in a new task.
- Claude Code's summarizer writes the summary after compaction. The line asks for a focus and points to the handoff file; it cannot force the result.
- When the skill cannot tell where it runs, it does not guess: it prints one neutral line with the next step for each surface.
- If it cannot tell which folder the work belongs to, it asks instead of guessing.
- After a compaction, Claude Code puts the text of invoked skills back into the context. The skill starts with a guard, so it acts only when your latest message is the command itself.
- It does not cover automatic compaction: if you do not run it, nothing happens.

## Alternatives

If you want every compaction, including automatic ones, steered without running a command, look at hook-based plugins such as [Better Compact](https://github.com/koenvdheide/prep-compact).

## How it was tested

The skill was installed as a plugin and run in headless Claude Code sessions, in a Cowork task and in a chat on claude.ai, in test projects separate from the author's own work, on Sonnet and Opus. Scenarios:

- a call in the middle of a stage, where the stage's tests are added only at its end
- a call after the stage is done
- a call while the work waits for the user's answer
- an empty session
- two task folders, with work tied to neither
- a project with no task documents
- a conversation in another language
- continuing after `/compact` with the printed line
- a direct request to Claude to run the skill by itself, which Claude Code refused
- a call when the work is already finished
- a stage interrupted in the middle of finishing, to check that the handoff was already on disk
- two sessions in the same checkout on the same day, two calls in one session, and two tasks in one session
- plans kept together in one folder, and a handoff file left by an earlier version
- a start in the home folder and at the root of a drive
- a project path with spaces on Windows
- a headless session given the signs of Cowork, with a connected folder and without one
- a headless session given the signs of a chat on claude.ai, and one given no sign of any surface
- a Cowork task with a connected folder: the command from the + menu, two calls in a row, a folder holding many projects where it asked which one, a task without a connected folder, and the word `context-left` without the add-on (in that run Cowork had no shell access to the computer and moved files through its upload and return)
- a chat on claude.ai: the command from the menu, the handoff as a file to download, the word `context-left`, and a new chat continuing from the handoff

A separate AI reviewer read the text, and an independent check went over the run logs. Tested with Claude Code 2.1.318.

## License

MIT. See [LICENSE](LICENSE).

Made by Ilya Balobanov · [bablobanov.com](https://bablobanov.com)
