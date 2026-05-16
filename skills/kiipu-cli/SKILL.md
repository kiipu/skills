---
name: kiipu-cli
description: Use when the user wants to manage Kiipu notes, authentication, local setup, or command help through the Kiipu CLI.
---

# Kiipu CLI

Use this skill when the user wants to manage Kiipu notes, Ask conversations, or CLI setup from Claude Code through the local `kiipu` CLI.

Primary cases:

- create, list, search, show, update, star, pin, delete, restore, or purge notes
- ask questions over saved notes, continue Ask conversations, list Ask history, or show an Ask conversation
- authenticate through browser login, no-browser login, a device name override, or an API key
- check auth status or log out
- run `kiipu doctor` to verify local setup and API reachability
- run `kiipu skills` to point the user to the separate Claude Code plugin package
- run `kiipu help`, `kiipu help note`, `kiipu help auth`, `kiipu help doctor`, or `kiipu help skills`
- explain the exact `kiipu` CLI command to use when the user asks for manual usage
- route explicit slash-command posting requests to `/kiipu:note` when the user wants the command form

Do not use this skill for:

- chat-context actions like deleting "the current note" without an explicit id
- website automation or API calls when the local CLI already covers the task

## Installation

```bash
npm install -g @kiipu/cli
```

## Required execution path

Execute the local Kiipu CLI instead of simulating results.

Note commands:

```bash
kiipu note list [--tag <tag>] [--sort <updatedAt|createdAt|title>] [--starred] [--deleted]
kiipu note search <query>
kiipu note search --query "<query>"
kiipu note show --id <noteId>
kiipu note update --id <noteId> --content "<text>" [--title "<title>"] [--visibility public|private] [--tags <a,b,c>]
kiipu note star --id <noteId>
kiipu note pin --id <noteId>
kiipu note create --content "<text>"
kiipu note create "<text>"
kiipu note delete --id "<noteId>"
kiipu note restore --id "<noteId>"
kiipu note purge --id "<noteId>"
```

Ask commands:

```bash
kiipu ask "<question>"
kiipu ask --question "<question>"
kiipu ask --conversation-id <conversationId> "<follow-up>"
kiipu ask "<question>" --top-k <1-20>
kiipu ask "<question>" --source-mode fresh|locked
kiipu ask history [--query "<query>"] [--limit <1-50>] [--archived]
kiipu ask show --id <conversationId>
```

Authentication:

```bash
kiipu auth login
kiipu auth login --device-name "<name>"
kiipu auth login --no-browser
kiipu auth login --api-key <cpk_...>
kiipu auth status
kiipu auth logout
```

Diagnostics and discovery:

```bash
kiipu doctor
kiipu skills
```

Help:

```bash
kiipu help
kiipu help ask
kiipu help note
kiipu help auth
kiipu help doctor
kiipu help skills
```

## Execution rules

1. Run the CLI before claiming success.
2. Return the CLI result accurately instead of inventing status.
3. If the user wants command usage rather than execution, provide the exact command form or the most relevant `kiipu help ...` command.
4. If the user wants to create or update a note, pass their full requested note body through `--content` or the positional create argument.
5. If the user wants show, update, star, pin, delete, restore, or purge but did not provide an id, ask for the explicit note id.
6. If the user wants search but did not provide a query, ask for the explicit search text.
7. If the user wants Ask but did not provide a question, ask for the explicit question text.
8. If the user wants to continue or show an Ask conversation but did not provide an id, ask for the explicit conversation id.
9. If authentication is missing, tell the user to run `kiipu auth login`.
10. Prefer `kiipu skills` when the user is asking where the Claude Code plugin or skill package lives.
11. Prefer the local CLI over direct HTTP calls so auth, logging, and environment selection stay consistent.
12. Keep host-specific runtime guidance out of Claude Code unless the user is explicitly working with that host.
13. If the user explicitly wants to use a slash command for posting, suggest `/kiipu:note <text>`.

## Examples

```bash
kiipu note list --sort updatedAt
kiipu note search "roadmap"
kiipu note show --id note_123
kiipu note update --id note_123 --content "Revised note" --visibility private --tags work,planning
kiipu note star --id note_123
kiipu note pin --id note_123
kiipu note create --content "Ship the beta today"
kiipu note create "Ship the beta today"
kiipu note delete --id note_123
kiipu note restore --id note_123
kiipu note purge --id note_123
kiipu ask "What did I save about the roadmap?"
kiipu ask --conversation-id conv_123 "What should I do next?"
kiipu ask history --limit 10
kiipu ask show --id conv_123
kiipu auth login
kiipu auth login --device-name "MacBook Pro"
kiipu auth login --no-browser
kiipu auth login --api-key cpk_example
kiipu auth status
kiipu auth logout
kiipu doctor
kiipu skills
kiipu help
kiipu help ask
kiipu help note
```
