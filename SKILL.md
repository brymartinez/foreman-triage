---
name: foreman-triage
description: Interview the maintainer about a feature or existing issue in GitHub or Linear, then publish the confirmed decisions to that issue.
argument-hint: "[GitHub repository/issue or Linear project/issue URL]"
disable-model-invocation: true
---

# Foreman triage

In Claude Code, invoke `/foreman-triage` with an optional URL. In Codex, invoke `$foreman-triage` with an optional URL, or select the skill from `/skills`. Use the URL supplied with the invocation (from `$ARGUMENTS` in Claude Code or the prompt in Codex) to select the path:

- No URL: interview a new feature and create an issue in the current checkout's GitHub repository.
- Repository URL such as `https://github.com/brymartinez/nest-starter`: interview a new feature and create an issue in that repository, regardless of the current checkout.
- Issue URL such as `https://github.com/brymartinez/nest-starter/issues/3`: interview about that existing issue and add the decisions there.
- Linear project URL such as `https://linear.app/acme/project/backend-123`: interview a new feature and create an issue in that project.
- Linear issue URL such as `https://linear.app/acme/issue/ENG-123/short-title`: interview about that existing issue and add the decisions there.

Accept only GitHub repository/issue and Linear project/issue URLs. Do not treat GitHub pull requests or other Linear pages as issues. The supplied URL identifies the target; never substitute another repository, project, workspace, or signed-in user's default. With no URL, use the current checkout's GitHub repository.

## Research before and during the interview

Gather relevant available context before asking the first round, and continue checking facts as new decisions expose gaps. Read the target issue and its discussion, repository instructions and design docs, nearby code and tests, relevant git history, related issues and pull requests, and repository knowledge when available. For a new feature, first locate the existing behavior and similar implementations. Follow links that could change the decision tree; stop when further sources would not change a question, recommendation, constraint, or acceptance criterion. Do not equate a large search result with useful context.

Separate verified repository facts, maintainer decisions, and tentative inferences in the interview. Resolve conflicting evidence before recommending a choice, or present the conflict to the maintainer when it is a product decision. Carry the decisions and implementation-relevant constraints into the confirmed issue or triage comment so Foreman's planner can work from that published record without this conversation. Do not publish raw research, private local material, or an unverified claim as a requirement.

## Existing GitHub issue URL

1. Read the issue title, body, and comments, including any prior triage notes. Use `gh issue view <URL> --json title,body,comments,state,url` or an authenticated GitHub tool. Identify what the issue already settles and what choices still need the maintainer's decision. Check relevant repository facts yourself when they affect a question.
2. Start the interview in this agent conversation after reading the issue. Follow the interview procedure below directly.
3. When the frontier is empty, draft the issue comment from the maintainer's answers. Include each settled decision, its relevant constraint or rationale, and any question the maintainer explicitly left open. Show the draft in the invoking chat and ask the maintainer there to confirm it reflects the shared understanding. This is the final interview check, not a separate request for permission to post.
4. As soon as the maintainer confirms the final draft, including any corrections, post one Markdown comment on the same issue headed `## Foreman triage decisions`. Do not end the skill after the interview summary or wait for another instruction to post. Preserve the issue body and existing comments. Check for an equivalent triage comment to avoid a duplicate, and reconcile any issue changes made during the interview. When using `gh`, write the comment to a temporary Markdown file and pass it with `gh issue comment <URL> --body-file <file>`.
5. Read the posted comment back. Then ensure the target repository has a `triaged` label and apply it to this issue, following **Mark confirmed GitHub issues as triaged** below. Return the comment URL and label result. Triage is complete only when the confirmed decisions are visible on the issue and the label is verified. If GitHub access or posting fails, keep the confirmed draft in the conversation, report the exact blocker, and state that triage is still incomplete. Preserve the issue body, state, assignees, and repository files.

## New GitHub feature, with no URL or a repository URL

1. Select the target repository. A supplied repository URL is authoritative. Without one, resolve the current checkout's GitHub `origin` remote to `owner/repo`; use `gh repo view --json nameWithOwner,url` only when it identifies the same current repository unambiguously. Do not infer the repository from the directory name or authenticated GitHub account. If the checkout has no clear GitHub repository, ask the maintainer for its URL while starting the feature interview.
2. Start the interview in this agent conversation immediately. Ask the first round of questions about the feature idea and the problem it should solve; the maintainer need not provide a written proposal first. Follow the interview procedure below directly. Check relevant facts in the selected repository as they become necessary, and keep the scope to one issue Foreman could implement.
3. When the frontier is empty, draft a concise issue title and body for the selected `owner/repo`. Write the body in plain English for a human reader: lead with a short description of the problem and desired behavior, then list observable acceptance criteria. Put settled decisions and the context an implementer needs under clear headings. Include rationale or constraints only where they affect implementation; cut repeated background and long explanations of straightforward choices. Link to existing code, comments, or documents when they carry useful detail. Keep any explicitly deferred questions visible. Do not present unresolved blocking design choices as settled or claim the issue is ready for implementation if they remain.
4. Show the complete title, body, and target repository in the invoking chat and ask the maintainer there to confirm the shared understanding. Once they confirm the final draft, including any corrections, create the issue without another permission prompt. Use `gh issue create --repo <owner/repo> --title <title> --body-file <file> --assignee "@me"` or an equivalent authenticated GitHub operation. Self-assignment matters: Foreman's inbox searches open issues assigned to the signed-in user. Do not start a Foreman delegation automatically.
5. Read the new issue back and verify its repository, title, body, open state, and self-assignment. Then ensure the target repository has a `triaged` label and apply it to the issue, following **Mark confirmed GitHub issues as triaged** below. Return the issue URL, label result, and say it is available to Foreman's inbox. If creation reports an error, check whether the issue was created before retrying so the feature does not become two issues. If GitHub access, creation, or assignment fails, keep the confirmed draft in the conversation, explain the exact blocker, and say whether an issue was created. The new-feature path is complete only when the confirmed description and decisions are visible on one self-assigned, labelled issue in the selected repository.

## Existing Linear issue URL

1. Use an authenticated Linear connection or the [Linear GraphQL API](https://linear.app/developers/graphql) with a personal key from `LINEAR_API_KEY`. Never ask the maintainer to paste a key into the chat or put one in a tracked file. For GraphQL, check response errors and mutation success even when HTTP succeeds, and paginate comments as needed. Resolve the issue identifier from the URL through Linear and verify the returned issue URL, workspace, title, description, status, project, labels, and comments. A URL alone is not proof that the issue is accessible. Identify what prior triage settled and check relevant facts yourself. If access is unavailable, report the blocker and keep any draft in this chat; do not switch to GitHub.
2. Interview in this conversation using the procedure below. When the frontier is empty, draft one `## Foreman triage decisions` comment covering settled decisions, material constraints, and questions explicitly left open. Show the complete draft and ask the maintainer to confirm it reflects the shared understanding.
3. After confirmation, re-read the issue and its comments to reconcile changes and avoid an equivalent triage comment. Post the confirmed Markdown as one Linear comment on that issue. Read the posted comment back, then apply and verify the `triaged` label as described below. Preserve the issue description, status, assignee, project, and other labels. Return the comment URL and label result. If any write fails, report what was published and the exact blocker; do not duplicate the comment while retrying a label.

## New Linear feature, with a project URL

1. Use the authenticated Linear connection or API described above to resolve the supplied project URL. Verify its workspace, project, and available teams. A Linear issue needs a team; when a project has more than one possible team and the feature context does not settle which one, ask the maintainer to choose during the interview. Do not infer a GitHub repository from the project URL.
2. Start the feature interview in this conversation. When the frontier is empty, draft a concise title and Markdown description for one issue in the selected project and team. Lead with the problem and desired behavior, then observable acceptance criteria and necessary implementation context. Keep explicitly deferred questions visible.
3. Show the full title, description, project, and team in this chat and ask the maintainer to confirm the shared understanding. After confirmation, create one Linear issue in that project and team, assigned to the authenticated user. If creation returns an ambiguous error, search the project for the exact title and draft before retrying.
4. Read the issue back and verify its title, description, project, team, status, and self-assignment. Apply and verify the `triaged` label as described below. Return the issue URL and label result. If creation or assignment fails, preserve the confirmed draft in this chat and report whether an issue was created.

## Mark confirmed Linear issues as triaged

Only after the confirmed comment or issue is published, look for an exact `triaged` label available to the issue's team. Create one for that team if none exists and permissions allow it; if creation fails, re-read labels before reporting failure. Add it without removing other labels, then read the issue back and verify the label. Do not change its status. If the label step fails, report the published issue or comment and the exact error. A Linear `triaged` label does not make the issue eligible for Foreman's inbox until Foreman's Linear integration is installed and configured.

## Mark confirmed GitHub issues as triaged

Only after the maintainer confirms the final draft and the comment or issue is published, check whether the exact `triaged` label exists in the target issue's repository. Create it if needed with `gh label create triaged --repo <owner/repo> --color 0E8A16 --description "Maintainer triage is complete; eligible for automatic delegation"`. If creation fails, check whether another actor created it before reporting a failure. Apply it with `gh issue edit <issue URL> --add-label triaged` unless it is already present. Read the issue back and verify the label. These steps are idempotent: never duplicate a comment or issue to retry a label operation. If creating or applying the label fails, report the exact error and that the published issue remains ineligible for Foreman's auto mode until it has the label. Do not change other labels, state, or assignees.

## Interview procedure

This procedure incorporates the text of Matt Pocock's `/grill-me` interview engine, `grilling`, under the MIT license in [LICENSE](LICENSE).

Interview the user relentlessly until you reach a shared understanding. Map this as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled: the questions you can ask _now_ without guessing at answers you haven't heard yet. Ask the whole frontier in one round: number each question and give your recommended answer. Deliver the round in the invoking chat, where the maintainer invoked this skill. You may use tools to prepare and display visual options for UI questions, but keep the question and the maintainer's decision in this chat. Do not move the interview to another chat or treat a preview, tool output, or preselected option as the maintainer's answer. End that turn after the round so the maintainer can reply in the same chat. Then wait for the user's answers before the next round.

Format a round like so:

```
❓ **Q1** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>

---

❓ **Q2** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>
```

For a UI decision where appearance or interaction affects the choice, show the maintainer concrete, labeled options under the relevant question. Use the available rendering or preview tools to display comparable mockups, screenshots, or prototypes in this chat; a textual list alone is insufficient when the visual difference matters. Keep the options at a comparable scale and show relevant states or interactions when those distinguish them. Briefly explain each option's tradeoff, recommend one, and ask the maintainer to choose or revise it. Inspect an existing UI first when consistency with it matters. Do not silently choose a design from your own mockups. If a visual cannot be rendered, say so and use a clearly labeled wireframe or precise description, then ask for the decision.

Carry the chosen UI behavior and constraints into the final issue draft. Include a durable visual reference when one is available to the implementer; do not rely on a temporary local preview as the only specification.

Each round the user answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier and ask the next round. A question whose answer depends on another question still open in this round belongs to a _later_ round, not this one.

Finding _facts_ is your job, never the user's. Investigate facts with the available tools; don't ask the user for anything you could look up yourself. The _decisions_ are the user's: put each to them and wait.

The session is done when the frontier is empty: every branch of the design tree visited, nothing left silently assumed. Do not act on it until the user confirms you have reached a shared understanding.

Invoking this skill authorizes publishing the confirmed comment or new self-assigned issue to the selected GitHub or Linear target, then creating and applying the `triaged` label there as needed. Do not publish partial notes or speculative answers.
