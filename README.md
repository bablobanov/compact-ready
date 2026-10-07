# Compact Ready

Compact Ready is a Claude Code command for the moment before `/compact`. It finishes the current stage of your plan, or stops where it needs your decision. Then it writes a handoff file next to your task and prints one ready-to-paste `/compact` line with a focus. It never runs `/compact` itself.

## Why

Automatic compaction starts late and writes a general summary. A `/compact` line with a clear focus gives a cleaner continuation, but you have to remember to write it. And when you remember, the work is often half done. Compact Ready closes the current piece of work first, saves the state to a file, and writes the focus line for you.

## What happens when you run it

1. **It says how it read the call.** The first sentence is one of three: the stage is done; the stage is not done and it will finish it; or it needs your decision first.
2. **It finishes the current stage or stops at a question.** It works until the current stage of your plan is done and the project's tests or build pass. It does not start the next stage. If going on needs your answer, approval, access or another outside action, it stops there.
3. **It writes a handoff file.** Sections: Done, Decisions and why, Changed files, Next step, Risks. The file goes next to your task or plan document. If the project has none, it writes `HANDOFF.md` in the project root. If it cannot tell which task folder to use, it asks and writes nothing.
4. **It prints one line and stops.** The last message is a single code block with a `/compact Focus on ...` line. Copy it, paste it, press Enter.

## Install

Once the plugin is listed in Anthropic's plugin directory, you can install it from there.

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

## Usage

Run it when you want to compact:

```
/compact-ready
```

If it cannot tell which task folder the work belongs to, it asks. Answer by running it again with the folder:

```
/compact-ready tasks/billing-refactor
```

Paste the line it printed. After compaction, tell Claude to go on:

```
continue
```

If another command already uses the name `/compact-ready`, run the plugin by its full name, `/compact-ready:compact-ready`.

If the command is not found after you install the plugin, run `/reload-plugins` or start a new session, and check in `/plugin` that the plugin is enabled. If something seems lost after compaction, open the handoff file: it records what was done, the decisions, the changed files, the next step and the risks. Check details against your files and task documents. Report problems in the repository's [Issues](https://github.com/bablobanov/compact-ready/issues).

## What it does not do

- It does not run `/compact`.
- It adds no hooks and runs no scripts of its own.
- It has no network code of its own and sends nothing by itself.
- Claude cannot start it on its own. The skill sets `disable-model-invocation: true`, so only you can run it.
- It does not start the next stage of your plan.

## Privacy

Compact Ready is a single instruction file with no code of its own, and it sends nothing by itself. While it runs, Claude reads your plan, todo list and task files as part of the normal session. To finish the stage it may edit project files and run your project's tests, under your permission settings. Then it writes a Markdown handoff file in your project. See [PRIVACY.md](PRIVACY.md).

## Limitations

- Claude Code only. Chat on claude.ai has no `/compact` command. Cowork and the Claude desktop app are not tested.
- Claude Code's summarizer writes the summary after compaction. The line asks for a focus and points to the handoff file; it cannot force the result.
- Tested with Claude Sonnet and Claude Opus models in the Claude Code command line.
- If it cannot tell which folder the work belongs to, it asks instead of guessing.
- After `/compact`, Claude Code puts the text of invoked skills back into the context. The skill starts with a guard, so it acts only when your latest message is the command itself.
- It does not cover automatic compaction. If you do not run it, nothing happens.

## Alternatives

If you want every compaction, including automatic ones, steered without running a command, look at hook-based plugins such as Better Compact.

## How it was tested

The skill was installed as a plugin and run in headless Claude Code sessions, in test projects separate from the author's own work, on Sonnet and Opus. Scenarios:

- a call in the middle of a stage, where the stage's tests are added only at its end
- a call after the stage is done
- a call while the work waits for the user's answer
- an empty session
- two task folders, with work tied to neither
- a project with no task documents
- a conversation in another language
- continuing after `/compact` with the printed line
- a direct request to Claude to run the skill by itself, which Claude Code refused

A separate AI reviewer read the text, and an independent check went over the run logs.

## License

MIT. See [LICENSE](LICENSE).

Made by Ilya Balobanov · bablobanov.com
