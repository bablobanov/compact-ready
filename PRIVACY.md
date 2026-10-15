# Privacy Policy

Last updated: 2026-10-15

Compact Ready is a Claude Code plugin made of one instruction file (a skill). It has no code of its own: no scripts, hooks, MCP servers or network requests, and the author runs no server for it.

## What it reads

When you run it, Claude reads what it needs to finish the current stage and describe it: your plan, todo list, task files and the project files used in the session. This is the same access Claude already has in your session, under your permission settings.

## What it changes on your disk

- To finish the current stage, Claude may edit project files and run your project's own test or build command, as in any session and under your permission settings.
- It writes a Markdown handoff file next to your task or plan document (`handoff-YYYY-MM-DD.md`, or `<plan name>.handoff.md` next to a plan whose file name names the task), or `handoff-YYYY-MM-DD-<task slug>.md` in the project root (the folder the session runs in) when the project has no task documents, or in the folder you named as the argument, even when that folder is outside the project. When it is going to finish an unfinished stage, it writes the file before finishing and updates it after. If the session worked on several tasks, it keeps one current handoff per task and, on a later call, writes only the tasks whose state changed. In an empty session, in your home folder or at the root of a drive without a folder argument, or when it has to ask which folder or plan file to use, it writes nothing; once you name a folder, the handoff goes there.

A handoff file can contain whatever your session worked on, such as file names, decisions and error messages.

## What it sends

The plugin sends nothing by itself. Claude Code sends your conversation, including the files Claude reads and the results of its tools, to its model provider as usual; that is covered by the terms and privacy policy of Claude Code, not by this plugin. Your project's test or build command can use the network on its own, as it would without this plugin.

## What the author collects

Nothing automatically: there is no telemetry, analytics or account. If you open a GitHub issue or send an email, the author receives what you choose to send, and GitHub or your email provider handles it under their own policies.

## How long data is kept

Handoff files stay where they were written, in your project or in the folder you named, until you delete them. The plugin keeps nothing anywhere else.

## Contact

Open an issue at https://github.com/bablobanov/compact-ready/issues or write to info@bablobanov.com.
