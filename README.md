# Foreman triage

A skill for Claude Code and Codex that interviews you about a GitHub feature or issue, then publishes the decisions to the issue you confirm. It uses the GitHub CLI (`gh`), so sign in with `gh auth login` first.

## Install

Clone once, then link it into either or both agents:

```sh
git clone https://github.com/brymartinez/foreman-triage.git ~/.local/share/foreman-triage
mkdir -p ~/.claude/skills ~/.agents/skills ~/.codex/skills
ln -s ~/.local/share/foreman-triage ~/.claude/skills/foreman-triage  # Claude Code
ln -s ~/.local/share/foreman-triage ~/.agents/skills/foreman-triage  # Codex
ln -s ~/.local/share/foreman-triage ~/.codex/skills/foreman-triage  # Codex desktop
```

Run `/foreman-triage` in Claude Code or `$foreman-triage` in Codex. Optionally add a GitHub repository or issue URL. With no URL, the skill uses the current checkout's GitHub repository. Restart an already open agent session if it does not see the new skill.

To update, run `git -C ~/.local/share/foreman-triage pull`.
