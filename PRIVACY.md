# Privacy Policy

Last updated: 2026-10-07

Compact Ready is a Claude Code plugin made of one instruction file (a skill). It has no code of its own: no scripts, hooks, MCP servers or network requests, and the author runs no server for it.

## What it reads

When you run it, Claude reads what it needs to finish the current stage and describe it: your plan, todo list, task files and the project files used in the session. This is the same access Claude already has in your session, under your permission settings.

## What it changes in your project

- To finish the current stage, Claude may edit project files and run your project's own test or build command, as in any session and under your permission settings.
- It writes a Markdown handoff file: in the task folder, or in the project root as `HANDOFF.md` (or a dated `handoff-YYYY-MM-DD.md` if `HANDOFF.md` is about other work). If the session worked on several tasks, it writes one in each task folder. In an empty session, or when it has to ask which folder to use, it writes nothing.

A handoff file can contain whatever your session worked on, such as file names, decisions and error messages.

## What it sends

The plugin sends nothing by itself. Claude Code sends your conversation, including the files Claude reads and the results of its tools, to its model provider as usual; that is covered by the terms and privacy policy of Claude Code, not by this plugin. Your project's test or build command can use the network on its own, as it would without this plugin.

## What the author collects

Nothing automatically: there is no telemetry, analytics or account. If you open a GitHub issue or send an email, the author receives what you choose to send, and GitHub or your email provider handles it under their own policies.

## How long data is kept

Handoff files stay in your project until you delete them. The plugin keeps nothing anywhere else.

## Contact

Open an issue at https://github.com/bablobanov/compact-ready/issues or write to info@bablobanov.com.
