# Company memory

The facts, rules and decisions this company works from, in an open-source CompanyOS the company owns. Read this file first, then the files it points at.

## What the company works on

- The current quarter's goal: `memory/goals.md` (not written yet — the first task in this repo is to write it).

## Where things live

| Folder | Holds |
|---|---|
| `memory/decisions/` | One file per decision: what was decided, the reason, the date. |
| `memory/glossary.md` | The words this company uses, and the meaning it means by them. |
| `finance/` | Purchase records and reconciliations. |
| `meetings/` | Notes from calls: decisions, owners, dates, open questions. |
| `work/` | Deliverables and `open-questions.md`. |
| `reviews/` | The weekly review. |

## How work lands here

Every change arrives as a change request on its own branch. A person reads the diff and merges it. That is the only path to `main`.
