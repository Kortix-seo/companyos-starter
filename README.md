# companyos-starter: a minimal open-source CompanyOS

This repository is a small CompanyOS you can run on Kortix and read end to end: one agent, two skills, the company memory and one weekly trigger, each committed beside the files it works on. Kortix is the open-source CompanyOS: your agents, their skills, your company memory, and every connector in one git repo you own, with the agents working on real cloud computers. Ten files with this one, all text you can open in an editor.

Plenty of things called a CompanyOS turn out to be a folder of documents a model reads at the start of a conversation. The difference here is what runs. The prompts in `agents/` are loaded by a session, the procedures in `skills/` are the steps that agent follows, and `memory/` is the record it has to check before it answers. A task ends with files in the repository and a change request a person reads, which is why the smallest one ships as code. Stated plainly: [what a CompanyOS is](https://companyosguide.com/what-is-a-company-os) comes down to which parts run.

## What is in it

The manifest declares both agents, and each is a plain prompt in `agents/`.

| File | What it gives you |
|---|---|
| `kortix.yaml` | The manifest: `default_agent: ops-desk`, the two-skill registry, both agents, one trigger |
| `agents/ops-desk.md` | The primary agent: read memory first, leave the result as a file, open a change request, never merge |
| `agents/weekly-review.md` | The subagent that writes the week's review into `reviews/` |

Both skills are folders under `skills/`; the memory is three files under `memory/`.

| File | What it gives you |
|---|---|
| `skills/invoice-reconcile/SKILL.md` | Match a supplier invoice against `finance/purchases.csv` and write `finance/reconciliations/<invoice-number>.md` |
| `skills/meeting-notes/SKILL.md` | Turn a transcript into `meetings/<yyyy-mm-dd>-<slug>.md`: decisions, owners, dates, open questions |
| `memory/MEMORY.md` | The index of what the company knows, and the folder each kind of file lives in |
| `memory/glossary.md` | The words this company gives its own meaning, such as `quarter close` and `within tolerance` |
| `memory/decisions/2026-10-01-pricing.md` | One decision on file: the list price, the reason, the date, what it replaced |
| `.gitignore` | `.kortix/`, `node_modules/`, `.DS_Store` |

Each skill is a folder with a `SKILL.md`: a name, a description of when to use it, then steps and rules. The fuller list of parts sits in [what a CompanyOS must contain](https://companyosguide.com/what-a-company-os-must-contain).

## Quickstart

Install the CLI and sign in. It supports macOS and Linux.

```
curl -fsSL https://kortix.com/install | bash
kortix login
```

Clone the project, then work inside the clone. The clone carries the manifest, so the agent sees the files you do.

```
kortix projects clone <project_id> companyos-starter
cd companyos-starter
```

Put the two documents the first task needs in the repository: the supplier invoice as `finance/invoices/acme-2026-10.pdf`, and the purchase record as `finance/purchases.csv`. The reconcile skill matches rows on the purchase reference, so the export needs that column.

Start a session and give it the job. The prompt names both documents, so the skill has its paths.

```
kortix sessions new --prompt "Reconcile finance/invoices/acme-2026-10.pdf against finance/purchases.csv"
```

A session needs a model. Any model provider with your own keys. Or the ChatGPT plan you already pay for.

The agent reads `memory/` first, so the tolerance rule in `memory/glossary.md` applies, not a guess. It writes `finance/reconciliations/acme-2026-10.md` with one row per invoice line, adds the invoice to `finance/reconciliations/open.md` if anything is over or missing, and opens a change request with the diff. Nothing is marked paid: payment stays a person's action outside the repository.

Read the diff, then merge it once the reconciliation reads the way you would write it.

```
kortix cr ls
kortix cr diff 1
kortix cr merge 1
```

`1` is the change request number that `kortix cr ls` printed. Session work reaches `main` through a change request. Merge is default-deny for agents. The reconciliation file is on `main`, and a person read it first.

## The weekly trigger

The `triggers:` block in `kortix.yaml` is the only scheduled thing in the repository. It fires at `cron: "0 8 * * 1"` in `UTC`, Monday morning, and it starts the `weekly-review` agent with a prompt rather than waiting to be asked.

That agent reads `memory/MEMORY.md`, then every decision file dated in the last seven days, then `git log --since="7 days ago" --stat`, and writes `reviews/<yyyy-mm-dd>.md`. The file has four sections: what shipped, what it cost, the week's decisions, and the open questions still in `work/open-questions.md`. It commits on its session branch and opens a change request without merging it. The report is a file in the repo, so the following Monday's review can read last week's.

## Make it your company's

Change the memory before you change anything else. `memory/glossary.md` decides what words mean when an agent reads a request, and `memory/decisions/` decides what has already been settled, so a stale entry will send the next session the wrong way. Replace the glossary with your own terms, write `memory/goals.md` (the file `MEMORY.md` points at), and add your decisions as `memory/decisions/<yyyy-mm-dd>-<slug>.md`. Then rewrite the two prompts for the work your company actually hands out, and keep the rule that a new decision file names the one it replaces.

Adding a skill takes three edits. Say you want a status report written every Friday from the week's commits. Create `skills/status-report/SKILL.md` with the same shape as the shipped skills: a name, a description of when to use it, then steps and rules.

Register the folder in the manifest's `skills:` map, which records that the skill exists and grants it to nobody.

```yaml
skills:
  invoice-reconcile: skills/invoice-reconcile
  meeting-notes: skills/meeting-notes
  status-report: skills/status-report
```

Grant it to the agent that should use it, in that agent's block of the same file.

```yaml
agents:
  ops-desk:
    skills: [invoice-reconcile, meeting-notes, status-report]
```

A trigger is the third edit. Add a second entry under `triggers:` with its own `slug`, an `on.cron` and an `on.timezone`, and a `run.agent` plus `run.prompt`, the way the weekly review does it.

## Self-host or cloud

Run it on Kortix Cloud, in your VPC, or on your own on-prem network. Self-host is free. The repository does not change with the choice. `kortix self-host init --domain <your domain>` writes the configuration and `kortix self-host start` brings it up on your own machine; a cloud project is the same clone, with sessions on machines you did not provision. Where your data has to stay decides it, and [self-hosting a CompanyOS](https://companyosguide.com/self-hosted-company-os) works through the rest. Secrets stay in the deployment, never in `kortix.yaml`.

## What a CompanyOS has to contain

The [CompanyOS comparison](https://companyosguide.com/companyos-comparison) lines the options up side by side. [companyosguide.com](https://companyosguide.com) sets out what a CompanyOS needs before it deserves the name: agents, the skills they run, the memory they check, and the review step that stops work landing unreviewed. This repository is the smallest version of that list you can clone and run. If the decision in front of you is between a CompanyOS and an agent platform, [the two answer different questions](https://companyosguide.com/companyos-vs-ai-agent-platform); if it is a rollout to a whole team, [CompanyOS for teams](https://companyosguide.com/companyos-for-teams) covers who reviews what.

Who actually ships the system is the next question. A vendor can build and run a CompanyOS for you, and that is a real purchase; the alternative is a repository your own team reads and maintains. [Who ships a CompanyOS you own](https://companyosguide.com/who-ships-a-companyos-you-own) works through the responsibility that comes with each, and [Kortix vs ETAOS](https://companyosguide.com/kortix-vs-etaos) sets the open-source repo against a managed implementation on the same question.
