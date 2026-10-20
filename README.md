# Foreman triage

A skill for Claude Code and Codex that interviews you about a GitHub or Linear feature or issue, then publishes the decisions to the issue you confirm. For GitHub, sign in to the GitHub CLI with `gh auth login`. For Linear, use an authenticated Linear connection or set `LINEAR_API_KEY` in your environment.

## Install

Clone once, then link it into either or both agents:

```sh
git clone https://github.com/brymartinez/foreman-triage.git ~/.local/share/foreman-triage
mkdir -p ~/.claude/skills ~/.agents/skills ~/.codex/skills
ln -s ~/.local/share/foreman-triage ~/.claude/skills/foreman-triage  # Claude Code
ln -s ~/.local/share/foreman-triage ~/.agents/skills/foreman-triage  # Codex
ln -s ~/.local/share/foreman-triage ~/.codex/skills/foreman-triage  # Codex desktop
```

Run `/foreman-triage` in Claude Code or `$foreman-triage` in Codex. Optionally add a GitHub repository/issue URL or a Linear project/issue URL. A project or repository URL starts a new feature interview; an issue URL continues triage on that issue. With no URL, the skill uses the current checkout's GitHub repository. Restart an already open agent session if it does not see the new skill.

For UI choices, the skill shows labeled visual options in the invoking chat and waits for your selection before recording the decision.

After you confirm the final draft, the skill publishes it and applies the target's `triaged` label, creating the label if needed and permitted. GitHub issues can then be considered by Foreman's auto mode. Linear issues will become eligible when Foreman's Linear integration is implemented and configured. If the label step fails, the skill reports what was published and what remains incomplete.

To update, run `git -C ~/.local/share/foreman-triage pull`.
