# Cortana

Cortana was my coding-agent harness for [OpenCode](https://opencode.ai). It splits work between research, implementation, verification, and review, with one primary agent keeping things moving.

**OpenCode v1 only. Later major versions aren't supported.**

I've retired the harness and kept this copy for anyone who wants to look through it or adapt the approach. The agent prompts and config are here, but you'll need to sort out models and skills before using it. I haven't tested this public copy end to end.

## How it works

Select the `cortana` primary agent or use the slash command:

```text
/cortana Fix the failing parser tests. Do not commit or push.
```

Cortana checks what's being asked and which files it can change, then picks the agents needed for the task:

| Agent | Responsibility |
| --- | --- |
| Cortana | Coordinates the work, checks each handoff, and records decisions. |
| Scout | Answers a specific research question before work starts. |
| Implementer | Makes the changes and runs focused checks. |
| Verifier | Independently checks whether the result meets the request. |
| Reviewer | Looks for bugs and risks, then reports what needs fixing. |

Agents run one at a time. Code changes go from Implementer to Verifier, with research or baseline checks added where they're useful. Reviewer gets involved when there's a meaningful risk or you've asked for a review. Fixes go back to Implementer, then through the relevant checks again.

For larger tasks, Cortana keeps decisions, check results, and a short retrospective in `.opencode/runs/<ticket-or-slug>.md`. You can also ask for an SVG showing how the agents interacted.

The [primary agent definition](agents/cortana.md) has the actual workflow and approval rules. There's also a shorter [workflow overview](docs/cortana.md).

## Files

| Path | Contents |
| --- | --- |
| [agents/](agents/) | Primary agent and four supporting agents. |
| [commands/cortana.md](commands/cortana.md) | `/cortana` command, routed to the primary agent. |
| [context/opencode.example.json](context/opencode.example.json) | The task-permission settings to merge into your config. |
| [docs/cortana.md](docs/cortana.md) | Overview of the final workflow. |
| [docs/handoff-260723-cortana-agent-system.md](docs/handoff-260723-cortana-agent-system.md) | Early design notes. Some rules changed after this was written. |
| [history/](history/) | Sanitised commit metadata and historical patches. |
| [archive.json](archive.json) | Where the files came from, their hashes, and what changed for this public copy. |
| [SHA256SUMS](SHA256SUMS) | Checksums for the current public files. |

## Using it with OpenCode v1

You'll need OpenCode v1 installed, with provider access configured separately. Before trying the harness:

1. **Choose your models.** The supporting agents still have their original `model:` values. Replace them with models you have access to, or remove the overrides so they inherit the primary agent's model. See [OpenCode agents](https://opencode.ai/docs/agents/).
2. **Check the skills.** I haven't bundled the external skills. Reviewer expects `code-review`, and drafting Git or external-service text requires `author-git-content`. The allowlists also name `my-voice`, `test-analyzer`, `security-review`, `simplify`, `frontend-design`, `vercel-react-best-practices`, and `web-design-guidelines`. Add trusted equivalents or update the prompts and allowlists to suit your setup. Your project may need other skills too. See [OpenCode skills](https://opencode.ai/docs/skills/).
3. **Read the permissions.** Check the settings at the top of each agent file and the limits below before giving the harness access to a project.

To use it in one project, copy the five `agents/*.md` files into that project's `.opencode/agents/` directory and `commands/cortana.md` into `.opencode/commands/`. For a global setup, use the same directories under `~/.config/opencode/`. Check for existing files with the same names before copying. See [OpenCode commands](https://opencode.ai/docs/commands/).

Merge [context/opencode.example.json](context/opencode.example.json) into the project's `opencode.json` or your global `~/.config/opencode/opencode.json`. Keep your other settings. The `cortana-*` task rule stops other agents from delegating to these roles; Cortana's own settings allow it to do so. See [OpenCode configuration](https://opencode.ai/docs/config/).

Restart OpenCode and run `opencode agent list` to check the agents were picked up. Try a task in a disposable project first. You'll need to select Cortana explicitly, as the example doesn't make it your default agent.

The documentation links above may change as OpenCode develops. They don't imply support for versions after v1.

## Permission and privacy limits

The prompts ask for approval before commits, pushes, PRs, destructive actions, worktrees, package installs, and external services. Don't treat that as a sandbox.

The supporting agents have broad Bash access, including the roles described as read-only. `edit: deny` blocks the edit tool, but a shell command can still change files or read credentials. `hidden: true` only hides an agent from autocomplete. Check the effective [OpenCode permissions](https://opencode.ai/docs/permissions/) before using this with sensitive projects.

Keep credentials outside this repo, in your provider's authentication setup. Run records can include project paths, task details, and command output, so `.gitignore` excludes them along with environment files, private keys, and local config. You'll still need to check anything you deliberately add to Git.

There are no package dependencies or install scripts here. If you add npm packages while adapting it, read [npm security best practices](https://github.com/lirantal/npm-security-best-practices), especially the advice on reviewing dependencies and controlling lifecycle scripts.

## Archive and integrity

This copy comes from [my dotfiles repo](https://github.com/adamcbrewer/dotfiles). The original workflow was created on 23 July 2026, last changed on 28 September 2026, and removed on 12 October 2026. I've kept the source commit and blob IDs in `archive.json` so the files can be traced back to that snapshot.

I've removed personal author and committer details and a shared conversation link from the history, and replaced the unrelated global OpenCode settings with the smaller example. The historical patches include those redactions. The agent and command definitions are unchanged.

To check the current files, run this from the repo directory:

```sh
sha256sum -c SHA256SUMS
```

The checksum list covers every public file except itself. It can tell you whether a file has changed, but it doesn't prove who wrote it or whether it's safe to run.

## Licence

I haven't chosen a licence for this archive yet.
