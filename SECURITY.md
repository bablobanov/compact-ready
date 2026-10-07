# Security Policy

## Supported versions

Only the latest release receives fixes.

## Reporting a vulnerability

Please do not report security problems in public issues.

- Preferred: open a private report on GitHub (Security tab → Report a vulnerability): https://github.com/bablobanov/compact-ready/security/advisories/new
- Or email info@bablobanov.com with "compact-ready security" in the subject.

Please include what you found, how to reproduce it, the plugin version and your Claude Code version.

## What to expect

I confirm receipt within two working days, investigate, and keep you updated. A confirmed problem is fixed in a new release. I credit reporters in the changelog unless they ask not to be named.

## Scope

Compact Ready is one instruction file with no code of its own. In scope:

- text in the skill that could lead Claude to act against your instructions or permission settings, write outside your project, or expose data
- problems in the plugin or marketplace manifest

Out of scope:

- Claude Code itself: report to Anthropic under its Responsible Disclosure Policy (https://www.anthropic.com/responsible-disclosure-policy)
- your project's own test or build commands
