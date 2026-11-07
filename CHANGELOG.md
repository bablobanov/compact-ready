# Changelog

## 1.2.0 (unreleased)

- Cowork: the skill writes the handoff into the folder connected to the task and prints how to continue in a new task. With no connected folder it says so instead of writing into the container.
- Chat on claude.ai: the skill finishes the stage and hands off for a new chat; it prints no `/compact` line there.
- The skill tells the three surfaces apart by fixed signs; when none applies it writes the handoff and prints one neutral line instead of guessing.
- The `/compact` line in Claude Code is unchanged.
- An optional add-on, a separate plugin named in the README, brings the context figures and, in Cowork, the word `context-compact`. The skill works without it as before. With it, the line of the Cowork block that tells you to send the word is in your language; the labels and the word stay fixed.
- What the skill says to you is in the formal form of address, and the add-on is named Compact Ready Context.
- Description, keywords, README, PRIVACY and SECURITY updated. Tested with Claude Code 2.1.318 in the command line, in Cowork and in the chat on claude.ai.

## 1.1.0 (2026-10-15)

- The skill looks around before it speaks: plan, current stage and handoff folder first, then the announcement.
- "Next step" and the /compact line can say that the work is finished and the next decision is yours. The skill no longer invents a step that is in neither the plan nor your request.
- When the stage is not done, the handoff is saved first, marked "in progress", and updated at the end.
- When the stage clearly would not fit in the remaining context, the skill stops and records the first unfinished item instead of asking you to decide.
- Handoff files are named after the task: `handoff-YYYY-MM-DD.md` in a task folder, `<plan name>.handoff.md` next to a plan whose file name names the task, `handoff-YYYY-MM-DD-<task slug>.md` in the project root instead of `HANDOFF.md`. An existing `HANDOFF.md` about the same work is updated in place.
- In a home folder or at the root of a drive, the skill asks where to write instead of writing there. The argument may also be a plan file.
- Paths in the /compact line and in the handoff are relative to the folder the session runs in, with forward slashes; a handoff in a folder named outside it keeps the form you typed, with forward slashes, and any other outside file keeps the path the tools reported.
- README: how updates work by default and `/reload-plugins`; new test scenarios.
- Remove .gitattributes so the plugin directory accepts new versions.

## 1.0.2 (2026-10-15)

- README: link to the Claude plugin directory listing and how to install from it.
- README: how to get a new version when installed from this repository, and a note on committing handoff files.
- README: header image. The skill is unchanged.

## 1.0.1 (2026-10-07)

- Add directory listing icon.

## 1.0.0 (2026-10-07)

- First public release.

## 0.1.0 (2026-10-07)

- First version of the `compact-ready` skill for Claude Code: finishes the current plan stage or stops at a question for the user, writes a handoff file, and prints one ready-to-paste `/compact` line.
- Plugin manifest, marketplace file, README, privacy policy and MIT license.
