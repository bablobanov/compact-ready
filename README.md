# Compact Ready

![Compact Ready: finish the stage, save the handoff, compact with focus](https://raw.githubusercontent.com/bablobanov/bablobanov/main/assets/compact-ready.jpg)

Listed in the [Claude plugin directory](https://claude.ai/customize/plugins/id/1eb4a2b3-703e-435e-8079-1134b644582d%40anthropic-plugin-directory)

Compact Ready is a plugin for the moment before compaction, for Claude Code, Cowork and the claude.ai chat. It finishes the current stage of your plan, or stops where it needs your decision or where the stage would not fit in the remaining context. Then it writes a handoff file next to your task and prints what comes next: in Claude Code one ready-to-paste `/compact` line with a focus, in Cowork the word to send, in a chat how to continue in a new chat. It never compacts on its own: in Claude Code you paste the line, in Cowork you send the word `context-compact`, in a chat you take the handoff to a new chat.

## Why

Automatic compaction starts near the context limit and writes a general summary. A `/compact` line with a clear focus gives a cleaner continuation, but you have to remember to write it. And when you remember, the work is often half done. Compact Ready closes the current piece of work first, saves the state to a file, and writes the focus line for you.

## Where it works

| Surface | The skill | The mod |
|---|---|---|
| Claude Code (command line, desktop app, editors) | finishes the stage, writes the handoff, prints the `/compact` line | adds the context figures to `/compact-ready`, the words `context-left` and `context-compact`, the slash commands `/context-left` and `/context-compact`, and the hints |
| Cowork | finishes the stage, writes the handoff into the folder connected to the task, prints the word to send | adds the figures, the words and the hints |
| Chat on claude.ai (web, desktop, mobile) | finishes the stage and hands off for a new chat; there is no `/compact` to run there | not loaded: plugins' hooks and mods do not run in chat, as Anthropic's platform support page says, so no figures, no words, no hints |

When the skill cannot tell where it runs, it writes the handoff and prints one neutral line with the next step for each surface instead of guessing.

## What happens when you run it

1. **It looks around first.** Before it says anything, it reads your plan, todo list and task files to find the current stage, whether the work is waiting on you, and the folder for the handoff file.
2. **It says how it read the call.** Normally the first sentence is one of five: the stage is done; the whole work is finished; the stage is not done and it will finish it; the stage will not fit in the remaining context; or it needs your decision first. If it cannot tell which task folder or plan file to use, or the working directory is your home folder or the root of a drive, it skips this and only asks which one to use.
3. **It finishes the current stage or stops at a question.** It works until the current stage of your plan is done and the project's test or build command passes, or it notes that the project has none. It does not start the next stage. If going on needs your answer, approval, access or another outside action, or a failing check cannot be fixed within this stage, it stops there and records why. The same when the stage clearly would not fit in the remaining context; "Next step" is then the first unfinished item of the stage. When it is going to finish an unfinished stage, it saves the handoff marked "in progress" before it starts and updates it at the end, so an automatic compaction in the middle still finds the state on disk.
4. **It writes a handoff file.** Sections: Done, Decisions and why, Changed files, Next step, Risks. The file is named after the task: `handoff-YYYY-MM-DD.md` in a task's folder; `<plan name>.handoff.md` next to a plan whose file name names the task, as in `docs/superpowers/plans/`; `handoff-YYYY-MM-DD-<task slug>.md` in the project root (the folder the session runs in) when the project has no task documents. A later call about the same task on the same day updates the same file; a new task gets its own. It never edits a handoff about other work. "Next step" is the first action from your plan or request; when the work is finished, it says so instead of inventing one.
5. **It prints one block and stops.** In Claude Code the last message is a single code block with a `/compact Focus on ...` line: copy it, paste it, press Enter. In Cowork the block names the handoff file and the word to send, `context-compact`; where the mod is not loaded, it says how to continue in a new task instead. In a chat on claude.ai the block names the handoff and how to continue in a new chat. Paths inside your project folder (the folder the session runs in) are relative to it, and a handoff in a folder you named outside it keeps the form you typed, with forward slashes; in Cowork and in a chat, paths are as the tools report them.

In an empty session, with no work before the call, it says so in one line and does nothing else.

## Install

From the Claude plugin directory: open the [listing](https://claude.ai/customize/plugins/id/1eb4a2b3-703e-435e-8079-1134b644582d%40anthropic-plugin-directory) and click **Add**. Claude Code loads it in sessions where you are signed in with the same claude.ai account.

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

Cowork has no `/compact` command; the plugin's mod gives you two words instead, sent as whole messages. `context-left` shows the context fill as the engine measures it. `context-compact` compacts with the focus of the latest handoff.

Run `/compact-ready` as usual; its last block names the handoff and tells you to send `context-compact`. The compaction starts after Claude's one-line reply and runs silently; the result shows with your next message. Handoff files go to the folder connected to the task, never into the session's container.

Where the mod is not loaded (an organisation's managed settings can keep it out), the block tells you how to continue in a new task; the words then do nothing.

## Context words

| Word | What it does | Where |
|---|---|---|
| `context-left` | shows the context fill as the engine measures it, the auto-compaction point and the last compaction the mod saw | Claude Code, Cowork |
| `context-compact` | compacts now, with the focus line of the latest `/compact-ready` in this session, or without a focus when there is none | Claude Code, Cowork |

Both work only as the whole message: a word inside a sentence does nothing. In Claude Code the same two exist as `/context-left` and `/context-compact`. In a chat on claude.ai the mod is not loaded, so the words do nothing there.

When the context reaches three quarters of the auto-compaction point the engine reports, the mod asks Claude to suggest `/compact-ready` once the current step is done; from nine tenths it repeats the suggestion once and keeps a note of the latest handoff beside every message. After a compaction it noticed, your next message carries a note naming the latest handoff, so Claude reads it before going on. The mod notices a compaction by the engine's own counter: a compaction that frees little (a short conversation under a large system prompt) can go unnoticed until the next measurement, and a session you resume is not mistaken for one.

## What it does not do

- It never compacts on its own: compaction starts only from your `context-compact`, your `/context-compact`, or the line you paste.
- Its mod runs only inside Claude Code's plugin runtime: no network, no file reads, no file writes of its own, no tools for Claude, nothing drawn on screen. It never sends a message as you.
- It has no network code of its own and sends nothing by itself.
- Claude cannot start it on its own. The skill sets `disable-model-invocation: true`, so only you can run it.
- It does not start the next stage of your plan.
- It does not invent a next step. When the work is finished, the handoff and the line say so.

## Privacy

Compact Ready is one instruction file and one small hooks module, and it sends nothing by itself. While it runs, Claude reads your plan, todo list and task files as part of the normal session. To finish the stage it may edit project files and run your project's tests, under your permission settings. Then it writes a Markdown handoff file in your project, or in a folder you named as the argument. The mod reads your messages only to recognise the two words and the command, keeps a small store in the plugin's own storage of the session's environment (the handoff path and focus line, the context figures), and writes no files. See [PRIVACY.md](PRIVACY.md).

## Limitations

- Tested with Claude Code 2.1.318 in the command line and in Cowork (the desktop app), on Claude Sonnet and Claude Opus models, and in a chat on claude.ai in the browser (the skill alone). Other versions were not tested.
- In a chat on claude.ai there is no `/compact` and the mod does not load: the skill hands off for a new chat, and the chat summarises on its own when it needs to.
- Claude Code's summarizer writes the summary after compaction. The line asks for a focus and points to the handoff file; it cannot force the result.
- The auto-compaction point the mod shows is the one the engine reports; a managed environment can compact earlier.
- In Cowork the compaction runs silently: Claude tells you the result with your next message.
- Where the mod is not loaded, for example under managed settings that keep mods out, the skill works alone and the words do nothing.
- When the skill cannot tell where it runs, it does not guess: it prints one neutral line with the next step for each surface.
- If it cannot tell which folder the work belongs to, it asks instead of guessing.
- After a compaction, Claude Code puts the text of invoked skills back into the context. The skill starts with a guard, so it acts only when your latest message is the command itself.
- Without the mod it does not cover automatic compaction: if you do not run it, nothing happens. With the mod, Claude is reminded before the automatic compaction and told where the handoff is after it.

## Alternatives

If you want every compaction steered without running a command, look at [Better Compact](https://github.com/koenvdheide/prep-compact), which steers every compaction through a hook; in our Cowork test the engine did not hand that event to plugins.

## How it was tested

The skill was installed as a plugin and run in headless Claude Code sessions, in test projects separate from the author's own work, on Sonnet and Opus; the mod's tests run against the engine itself (`claude plugin test`). Scenarios:

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
- the two words sent as whole messages, and the same words inside a sentence
- the hint bands at three quarters and nine tenths of the auto-compaction point, with and without auto-compaction
- the focus taken only from the turn that ran the skill, with decoy blocks, long lines and odd characters
- a session resumed after a compaction request that never happened
- a Cowork task with the mod and without it, with a connected folder and without one
- a chat on claude.ai with the skill alone

A separate AI reviewer read the text and the code, and an independent check went over the run logs. Tested with Claude Code 2.1.318.

## License

MIT. See [LICENSE](LICENSE).

Made by Ilya Balobanov · [bablobanov.com](https://bablobanov.com)
