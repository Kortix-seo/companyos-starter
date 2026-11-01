---
name: meeting-notes
description: Turn a meeting transcript into the four things a company needs from it — decisions, owners, dates, open questions — as a file in the repository. Use after a call, a recording or a pasted transcript.
---

# Meeting notes

How this open-source CompanyOS turns a transcript into a file the company keeps.

## Steps

1. Read the transcript. If it has no speaker labels, keep the quotes and drop the guesses about who said what.
2. Write `meetings/<yyyy-mm-dd>-<slug>.md` with exactly four sections:
   - **Decisions.** Each one in a single sentence, in the past tense, attributed to the person who decided it. A discussion is not a decision.
   - **Owners.** One line per thing someone agreed to do: the person, the thing, the date they gave. No date means `no date given`, never an invented one.
   - **Numbers.** Every figure stated, with the sentence it came from.
   - **Open questions.** What was left unanswered, phrased as the question someone has to answer.
3. For each decision, write `memory/decisions/<yyyy-mm-dd>-<slug>.md` with the decision, the reason as it was given, and a link to the notes file.
4. Commit the files on your session branch and open a change request.

## Rules

- Never summarise without a file. A summary in chat is lost; a file is the company's memory.
- Never attribute a decision to someone the transcript does not name.
- A decision that contradicts an existing file in `memory/decisions/` gets a new file that names the one it replaces.
