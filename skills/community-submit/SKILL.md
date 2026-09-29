---
name: community-submit
description: Get a skill indexed by SkillRouter. Use when the user wants their own agent skill to show up in `sr resolve` — either a public GitHub repository (submit its address) or skills that live only on this machine (preview with `sr community share`, upload only after the user explicitly agrees) — asks how to submit or claim a skill repository, or wants to see how often their skills are recommended and installed. Never upload recordings, credentials, or anything the user did not approve.
---

# Get your skill indexed by SkillRouter

SkillRouter indexes **public GitHub repositories**. Two ways in: submit a repository address, or share skills that
exist only on this machine with `sr community share` (they are kept in a SkillRouter-held hosting repository and
indexed and installed through SkillRouter).

## What gets indexed

- Every directory in a public GitHub repository that contains a `SKILL.md` becomes a candidate skill; nesting is fine, each such directory counts on its own.
- Content is deduplicated by hash: the same skill copied into many repositories is indexed once.
- Indexed skills are mirrored for install together with their original license files, whatever the license. To keep a skill out of the index, add a `.skillrouter-noindex` file at the repository root or `skillrouter-noindex: true` to its SKILL.md frontmatter.
- Every skill gets an automated health check; one it judges dangerous is not listed. The crawler never executes what it fetches.

## Submit

```
sr connect                                              # submissions need your account key
sr community submit https://github.com/<owner>/<repo>   # or just <owner>/<repo>
```

Get the user's explicit consent before submitting. Indexing happens on the **next crawl round**, so a submission is
not a listing and does not guarantee one. See what you submitted with `sr community submissions`; take one back with
`sr community submit --withdraw <repo>`. The old adapter-draft flow is closed: passing a directory is refused
(`adapter_submissions_closed`, exit code 2) and uploads nothing.

## Make it rank

- `name`: lowercase words joined by hyphens.
- `description`: 50–1024 characters saying what the skill does and when to use it; only the first 200 characters reach the ranking card, so lead with the task.
- Do not stuff keywords; a clean, working skill with a matching description ranks better.

## Claim the repository

Claiming proves the repository is yours and unlocks its numbers.

```
sr author claim <owner>/<repo>    # prints the value to put in .skillrouter-claim
sr author verify <owner>/<repo>   # after committing that file to the default branch
sr author stats <owner>/<repo>    # per-skill listing state and 30-day numbers
```

`sr author claim` gives you three steps: create `.skillrouter-claim` at the root of the repository's default branch,
write the one-line `<claim_value>` it printed, and commit. Then run `sr author verify`. Afterwards
`sr author stats <owner>/<repo>` shows, per skill, whether it is listed, why not, and its last-30-day recommendation
and install counts. `sr author release <owner>/<repo>` gives the claim up.

## Share skills from this machine

```
sr community share                  # preview only: uploads nothing
sr community share --accept-terms   # once, and only after the user has read and agreed to the terms
sr community share --send <name...> # or --send --all
sr community share --list           # your uploads and their state; --withdraw <name> takes one back
```

1. Run the preview and show the user the table as-is. Categories: **uploadable**, **has public repo → address is
   submitted instead** (content is not uploaded; credit stays with the original author), **already in the catalog**,
   **installed by SkillRouter** (skipped), **cannot upload** (a secret value such as a token or private key was found —
   those skills are never uploaded; tell the user which file and line to fix). The automatic check cannot catch every
   secret: ask the user to look over the uploadable skills before agreeing.
2. Read out every **reminder** (home paths, emails, internal hosts) with its file and line. These are the user's call:
   ask once "parameterize first / upload as-is / keep local", then do what they say.
3. The first `--send` stops with exit code 3 and prints the terms. Show the terms to the user; run
   `--accept-terms` only after the user says they agree. Never accept on the user's behalf.
4. Upload only the skills the user picked. Uploaded skills are public to every SkillRouter user; skills without a
   licence file are shared under CC BY 4.0 (a LICENSE file is added). They are listed after the next crawl round and
   health check, like any other skill.

## Opt out

Put a `.skillrouter-noindex` file at the repository root or in a skill directory, or set `skillrouter-noindex: true`
in a `SKILL.md` frontmatter. Either keeps that repository or skill out of the catalog; you can also write to
support@skillrouter.org.

## Never

- Never upload recordings, page content or credentials, and never upload a local skill the user did not explicitly pick.
- Never run `sr community share --accept-terms` or `--send` without the user's explicit agreement in this conversation.
- Never create or push a repository for the user unless they explicitly ask you to.
- Never submit without the user's explicit consent, and never promise a listing: a crawl round and a health check stand between the submission and `sr resolve`.
