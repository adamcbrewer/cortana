# Historical reference

I've kept these exports so you can see how the OpenCode v1 harness changed over time. Start with [the main README](../README.md) if you're looking to use it.

| File | Scope |
| --- | --- |
| [commits.txt](commits.txt) | Relevant source-branch commits in chronological order. |
| [changes.patch](changes.patch) | Revisions of the agents, slash command, overview, and early design handoff through deletion. |
| [config-permissions.patch](config-permissions.patch) | Introduction and removal of the shared Cortana task-permission rule. |
| [removal.patch](removal.patch) | Removal of the workflow and its configuration rule. |

I've replaced author and committer details with a generic contributor identity and removed a shared conversation link. Commit IDs, dates, subjects, and source paths are still here so you can trace the history. The blob IDs in the patch headers refer to the original files, before those redactions. Don't apply these patches to restore this public copy.

Some of the earlier rules changed, including mandatory commits and how verification worked. The [final primary agent](../agents/cortana.md) has the rules the harness ended up with. This archive supports OpenCode v1 only.
