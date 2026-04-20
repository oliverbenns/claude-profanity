---
name: claude-profanity
description: Search through Claude Code conversation history for prompts containing profanity. Use when the user wants to find or audit profanity in their past Claude conversations.
license: MIT
compatibility: Designed for Claude Code. Requires access to ~/.claude/history.jsonl.
allowed-tools: Grep Bash(grep:*) Bash(cat:*) Read
metadata:
  author: Oliver Benns
---

# Profanity Check Skill

Search through the user's Claude Code conversation history and project files for prompts that contain profanity.

## Data sources to search

1. **History file**: `~/.claude/history.jsonl` - contains all conversation prompts across projects
2. **Project session files**: `~/.claude/projects/*/` - contains detailed session data

## Default profanity word list

Search for these words (case-insensitive, word-boundary matched):

```
fuck, shit, damn, ass, bitch, crap, bastard, dick, piss, bullshit, wtf, stfu, goddamn, asshole, dumbass, dipshit, shitty, fucking, fucked, bitchy, crappy, dammit, hell
```

If the user passed additional words via `$ARGUMENTS`, add those to the search list as well.

## Scope

- If the user specifies a project name (e.g. "check profanity in my-app"), filter results to only entries whose `project` field contains that project name.
- If the user says "this project" or "current project", filter results to entries whose `project` field matches the current working directory.
- Otherwise, search across all projects (global).

## Instructions

1. Use Grep to search `~/.claude/history.jsonl` for the profanity word list (case-insensitive, word-boundary matching). Each line in this file is a JSON object with a `display` field containing the user's prompt, plus `project` and `sessionId` fields.

2. If a project scope was specified, filter results to only matching entries.

3. For each match found:
   - Extract the `display` field (the user's actual prompt text)
   - Extract the `project` field to identify which project it came from
   - Identify which profane word(s) were matched

4. Present results in a markdown table with columns:
   - **#** (row number)
   - **Project** (short project name extracted from the path, e.g. `bcb-apps` from `/Users/oliver/Code/bcb-apps`)
   - **Prompt** (the user's message, with the profane word(s) bolded)
   - **Word(s)** (which profane words were found)

5. End with a brief summary: total count, most-used word, which project had the most matches.

## Important

- Only report on USER prompts/messages, never on assistant responses.
- The history.jsonl file contains one JSON object per line with `display` as the prompt text.
- Keep the output concise and fun - this is a lighthearted audit.
