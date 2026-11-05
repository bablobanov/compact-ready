# Privacy Policy

Last updated: 2026-11-05

Compact Ready is a plugin made of one instruction file (a skill) and, since 1.2.0, one small hooks module (a "mod"). Beyond that module it has no code of its own: no scripts, MCP servers or network requests, and the author runs no server for it.

## What it reads

When you run it, Claude reads what it needs to finish the current stage and describe it: your plan, todo list, task files and the project files used in the session. This is the same access Claude already has in your session, under your permission settings.

## The mod

Since 1.2.0 the plugin also has a small hooks module (a "mod") that runs inside Claude Code's plugin runtime, in the environment of your session: on your machine in the terminal, in the session's container in Cowork. In a chat on claude.ai it does not load.

**What it reads.** The text of your messages, only to check whether a message is exactly the word `context-left` or `context-compact`, or a run of `/compact-ready` or `/compact`, and to tell which reply belongs to which message; the final text of Claude's reply in the turn that ran `/compact-ready`, only to find the block that command printed; and the context figures the engine already keeps (tokens used, window, auto-compaction point). It reads no files and no other sessions.

**What it writes.** Hidden notes beside your messages that only Claude reads: the figures, a suggestion to run `/compact-ready` when the context gets close to the auto-compaction point, and, after a compaction, the path of the latest handoff. It writes no files.

**What it keeps.** In the plugin's own store, a JSON file under the Claude Code configuration directory of the session's environment: per session, the handoff path and the focus line `/compact-ready` printed, cut to 2,000 characters (it describes your task, so it can contain file names, decisions and error messages, like the handoff file itself), when it was printed, the last context figures, which hints were given, the first 80 characters of your recent messages while the mod waits for their replies, pending compaction requests, and the last error of the mod. The last 20 sessions are kept; a session cleared with `/clear` is removed. Delete the plugin to remove the store, or delete the file. In Cowork the store lives in the session's container under Anthropic's environment, not on your disk.

**What it sends.** Nothing. It has no network code. The focus line goes to the engine's own compaction, the same place a pasted `/compact` line goes, and from there to the model with the rest of the conversation, under Claude Code's own terms.

## What it changes on your disk

- To finish the current stage, Claude may edit project files and run your project's own test or build command, as in any session and under your permission settings.
- It writes a Markdown handoff file next to your task or plan document (`handoff-YYYY-MM-DD.md`, or `<plan name>.handoff.md` next to a plan whose file name names the task), or `handoff-YYYY-MM-DD-<task slug>.md` in the project root (the folder the session runs in) when the project has no task documents, or in the folder you named as the argument, even when that folder is outside the project. When it is going to finish an unfinished stage, it writes the file before finishing and updates it after. If the session worked on several tasks, it keeps one current handoff per task and, on a later call, writes only the tasks whose state changed. In an empty session, in your home folder or at the root of a drive without a folder argument, or when it has to ask which folder or plan file to use, it writes nothing; once you name a folder, the handoff goes there.

A handoff file can contain whatever your session worked on, such as file names, decisions and error messages.

## What it sends

The plugin sends nothing by itself. Claude Code sends your conversation, including the files Claude reads and the results of its tools, to its model provider as usual; that is covered by the terms and privacy policy of Claude Code, not by this plugin. Your project's test or build command can use the network on its own, as it would without this plugin.

## What the author collects

Nothing automatically: there is no telemetry, analytics or account. If you open a GitHub issue or send an email, the author receives what you choose to send, and GitHub or your email provider handles it under their own policies.

## How long data is kept

Handoff files stay where they were written, in your project or in the folder you named, until you delete them. The mod's store keeps the records of the last 20 sessions in the plugin's own storage of the session's environment until you delete the plugin or the file; `/clear` removes the current session's record. The plugin keeps nothing anywhere else.

## Contact

Open an issue at https://github.com/bablobanov/compact-ready/issues or write to info@bablobanov.com.
