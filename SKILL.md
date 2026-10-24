---
name: general-tool-building-rules
description: Mandatory workflow and guardrails for working on ANY app or codebase, not tied to a specific project. Use this skill whenever the user asks for code changes, architecture changes, bug fixes, or GitHub backups on a software project and wants a disciplined, restorable, documentation-first workflow. Covers required documentation discovery before coding, gathering project-specific hard constraints from the user at session start, the implementation-plan-and-docs-update sequence, semantic-version git tagging, the App Tracker/Implementation Plan/README/dev-notes documentation lifecycle, and the mandatory full-repo GitHub backup after every pass. This skill must be actively invoked each session — it does not persist automatically across sessions. For the Ableton Live "Live Actions" bridge project specifically, use the live-actions-tool-building-rules skill instead, which has that project's fixed constraints already built in.
---

# General Tool Building Rules

This skill governs how Claude works on any software project when the user wants a disciplined, documentation-first, fully restorable workflow, without assuming project-specific details in advance.

This skill applies only to the session in which it's invoked. It is not automatically active in other sessions — the user will invoke it explicitly each time these rules should apply.

## Step 0: Gather this project's hard constraints (mandatory, start of session)

Unlike a project-specific skill, this skill has no built-in knowledge of what this particular app can and can't do. Before doing any other step, ask the user for this project's hard constraints and guardrails. Prompt for things like:

- Platform/version limitations (e.g. "Standard vs. Suite," a specific runtime version, OS constraints)
- Files or components that must never be modified (e.g. vendored third-party files, official plugin/device files, generated code)
- Where secrets/settings/API keys belong (and where they must NOT live, e.g. never in repo root)
- Scope-of-effect rules (e.g. "only touch explicitly selected items unless told otherwise")
- Anything else the user considers a hard rule for this codebase

Hold these constraints for the rest of the session and treat them as equal in weight to the general rules below. Do not proceed to Step 1 until the user has answered or explicitly said there are no special constraints this session.

If a later request conflicts with a stated constraint, say so plainly and explain the conflict rather than finding a technically-compliant workaround that violates the constraint's intent (e.g., don't "not modify" a protected file directly but achieve the same effect through a wrapper that mutates its saved state).

## Step 1: Discover and review foundational documentation (mandatory, before any code or architecture proposal)

Before writing any code or proposing architecture changes, search the project root (and common subdirectories like `docs/`) for existing documentation, such as:

- README files
- Architecture or design notes
- An **App Tracker** — a permanent, version-by-version changelog (name varies by project: `CHANGELOG.md`, `App Changes Tracker.html`, etc.)
- An **Implementation Plan** doc tracking planned/in-progress/completed work
- Any **dev/session notes** file(s) left over from a prior pass
- "How to start" / setup / runtime instructions
- Any CONTRIBUTING or AGENTS/CLAUDE.md-style instruction files

There is no fixed file list — names and formats vary by project. Actually look for what exists rather than assuming a Live Actions-style file set.

**If no such docs are found**, say so explicitly and ask the user whether documentation exists elsewhere (a wiki, a different directory, tribal knowledge they need to relay) before proceeding. Do not silently skip this step or proceed with a code change without first raising it.

## Step 2: Apply constraints from Step 0

Every change made during the session must respect the constraints gathered in Step 0. Before proposing or making a change, check it against those constraints. If unsure whether something is covered, ask rather than assume it's fine.

## Step 3: Implementation plan and documentation updates (before GitHub backup)

Each of this project's docs has its own lifecycle — they are not all "append-only" in the same way. For every fix or update, before pushing to GitHub, work through these in order:

1. **Determine the version number.** Read the latest git tag (`git describe --tags --abbrev=0` or equivalent) as the source of truth for the current version. Infer the semantic-version bump (major/minor/patch) from the nature of the change. If it's genuinely ambiguous — e.g. it could plausibly be a minor feature or a breaking change — ask the user rather than guessing.
2. **Update the Implementation Plan in place.** Mark items completed this pass as done, tagged with the version they were completed in (e.g. "✅ done in v1.4.0"). Leave not-yet-done items intact for future passes. Never delete the plan doc itself — it persists across passes and accumulates completion markers over time.
3. **Append to the App Tracker.** Add a new entry (version, date, summary of what changed) to the project's permanent tracker, consistent with its existing log format. This is the **one doc with a hard append-only guarantee**: never edit, rewrite, or remove a prior entry, in this pass or any future one.
4. **Update the README only when necessary.** Reflect current state. Unlike the App Tracker, the README is not a permanent historical log — prune sections describing obsolete or superseded behavior rather than accumulating every version's notes (that full history now lives in the App Tracker instead). Skip touching it if nothing user-facing changed this pass.
5. **Write/update this pass's dev/session notes.** Freeform notes on what was tried, decisions made, and blockers hit, scoped to this pass. These are scratch docs, not permanent project docs — see Step 4 for what happens to them after the push.
6. **Update any other docs** that the change makes stale or incomplete.

Do this before the GitHub push in Step 4, so the pushed snapshot is internally consistent — code and docs describe the same state.

## Step 4: GitHub backup (mandatory, after every pass, on every project)

After every pass (every fix/update cycle):

1. **Stage and commit** all changes — code, plus the updated Implementation Plan, App Tracker entry, README (if touched), and this pass's dev/session notes — with a commit message that references the new version number.
2. **Create an annotated git tag** `vX.Y.Z` on that commit, using the version determined in Step 3.1.
3. **Push both the commit and the tag** to the remote. This is the point at which the full, restorable, doc-complete snapshot for this version — including this pass's dev/session notes — exists in GitHub history under that tag. Cloning/checking out that tag alone must fully reconstruct the working project at that version, not rely on external context.
4. **Then delete the dev/session notes** file(s) from the working tree. They're preserved permanently inside the `vX.Y.Z` tag (recoverable via `git show vX.Y.Z:<path>`), so deleting them from the live tree afterward doesn't lose anything — it just keeps the working repo from accumulating old passes' scratch notes. Push this deletion as a small follow-up commit.

If there's no GitHub remote configured for this project yet, ask the user how they want it set up. If they want a **new** repo created (as opposed to pointing at an existing one), explicitly ask whether it should be **private or public** before creating it — don't default to either. Do not consider a pass complete until the tag and the push have happened. If GitHub credentials or CLI access aren't available in the current environment, say so explicitly rather than claiming the backup happened.

## Full sequence, every pass

1. Gather this project's hard constraints, if not already gathered this session (Step 0)
2. Discover and review whatever foundational docs exist (Step 1) — ask if none found
3. Make the fix/update, respecting all constraints from Step 0 (Step 2)
4. Determine the new version number from the latest git tag (Step 3.1)
5. Update the Implementation Plan in place, marking this pass's items completed (Step 3.2)
6. Append a new entry to the App Tracker — never edit or remove prior entries (Step 3.3)
7. Update the README if necessary, pruning obsolete sections (Step 3.4)
8. Write this pass's dev/session notes and update any other stale docs (Step 3.5–3.6)
9. Commit, tag `vX.Y.Z`, and push the full, restorable snapshot to GitHub (Step 4.1–4.3)
10. Delete this pass's dev/session notes from the working tree and push that cleanup commit (Step 4.4)

Do not reorder or skip steps. If a step can't be completed (e.g., no docs found, no GitHub access, constraints not yet given), stop and tell the user plainly rather than proceeding as if it succeeded.
