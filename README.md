# Session Handoff

Session Handoff saves a concise progress file so a new Claude Code or Codex session can pick up ongoing work. Handoff files live in `.ai/session-handoff/` inside the project you’re working on.

## Install

Run the command for your tool in a terminal. These commands install the skill for your user account on this machine.

### Claude Code

```sh
mkdir -p "$HOME/.claude/skills" &&
  git clone https://github.com/low-earth-orbit/session-handoff.git "$HOME/.claude/skills/session-handoff"
```

Open Claude Code in your project and invoke `/session-handoff`. For a project-only install, use `.claude/skills/session-handoff` as the clone destination. [Claude Code skill locations](https://code.claude.com/docs/en/skills#choose-where-skills-live)

### Codex

```sh
mkdir -p "$HOME/.agents/skills" &&
  git clone https://github.com/low-earth-orbit/session-handoff.git "$HOME/.agents/skills/session-handoff"
```

Open Codex in your project and invoke `$session-handoff`. For a project-only install, use `.agents/skills/session-handoff` as the clone destination. [Codex skill locations](https://developers.openai.com/codex/skills#where-codex-loads-local-skills)

## When to use it

Use a handoff when:

- The conversation has grown long or prompt-cache benefits have expired, and restating the full history would be expensive.
- A session may end because of a limit or interruption, and you want the latest checkpoint available to resume.
- You want to continue in a new session or switch providers while keeping the same repository work available.

The skill cannot detect cache expiry or write after a session has stopped. For interruption recovery, turn on handoff updates while the session is still active; the file then reflects the latest saved checkpoint.

## Start a handoff

To save a snapshot and keep it updated as work proceeds, say:

> Do this task; ready to handoff.

The skill creates or updates `.ai/session-handoff/YYYY-MM-DD-<task-slug>.md` and refreshes it only at milestones: a completed work item (with its verification result) or a blocker. Decisions are folded into the next milestone update. It does not write after every edit or decision, or while idle, and it keeps the file under about 60 lines.

If you want a one-time snapshot only, ask to save a handoff without saying “ready to handoff.”

## Resume in a new session

You can name a file or ask the skill to find an outstanding one:

> Continue from handoff.

The skill searches for open handoffs. If there is one, it resumes immediately. If there are several, it asks which one. Periodic updates default to off in each new session unless you ask to enable them.

To choose directly:

- Resume without periodic updates: `Continue from <handoff-file>, no periodic handoff updates.`
- Resume and keep saving checkpoints: `Continue from <handoff-file>, ready to handoff.`

The new session checks the handoff against the current repository before continuing. In either update mode, it makes a one-time edit to record the receipt and update setting and correct any stale facts it can verify. The receipt leaves the handoff open. With periodic updates off, it skips milestone updates and still records completion or any required check that blocks it.

## What the file contains

The handoff records the task status, current progress, important decisions and constraints, relevant files, verification results (including anything unverified), blockers, and the next action. It includes the last updater and receiving agent/tool when those names are known.

Keep secrets and unrelated conversation out of the file. Handoff text is context for the task; current user and repository instructions still take precedence.

## When the task is done

The skill marks the handoff complete once the task meets your completion criteria and all checks required by you or the repository have passed. If a required check fails or cannot run and prevents completion, it keeps the handoff open and records the blocker. It reports any optional verification it skipped. Once the task is complete, the skill asks whether to keep or delete the handoff and deletes it only after you confirm.

Git visibility is your choice. The skill does not change `.gitignore` or stage, commit, or push handoff files.

## Expected savings

A concise handoff can reduce the context a new session needs compared with replaying a long conversation, especially when the prior discussion is much larger than the handoff. It does not preserve or extend a prompt cache, and it does not guarantee lower total token use or cost: writing and reading the handoff and checking the repository also use context. The benefit is greatest after a long session and may be small for a short task.

## License

This project is licensed under the [MIT License](LICENSE).
