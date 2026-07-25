# How to add rates (or any unpublished fact) safely

Rates are the #1 customer question and the biggest gap in this agent. Adding them is the
single highest-value change you can make — **once the owner confirms the real numbers.**

> ⛔ **Do not add rates from anywhere else.** Not from a competitor, not from a booking site,
> not from an old quote, not from memory, not from an estimate. A wrong rate is worse than no
> rate: the customer arrives expecting a price Trac never agreed to, and staff have to break
> the bad news at the counter.

## The rule

A fact is safe to add when it comes from **Trac's owner or their official website**, and
someone at Trac has confirmed it is current. Everything else stays on the never-answer list.

## Step 1 — get the numbers

Send [`../OPEN-QUESTIONS.md`](../OPEN-QUESTIONS.md) to the owner. For rates specifically, get:

- Daily rate for each of the six categories
- Weekly / monthly rate or discount, if any
- **Currency — USD or AWG** (the website never says, so this must be confirmed)
- Whether tax is included, and what's added if not
- Minimum rental period
- High-season dates and any surcharge

**Get all of these together.** A daily rate without a currency, or without knowing if tax is
included, is not usable — it just produces a different dispute.

## Step 2 — update the knowledge file

Add the confirmed figures to `knowledge/02-fleet.md`, and note the date and who confirmed
them. Delete the matching lines from `knowledge/99-unverified.md`.

## Step 3 — update the prompt (three edits, all required)

In `AGENT_PROMPT.md`:

1. **Add the rates** to the APPROVED KNOWLEDGE section, e.g. under the fleet table:

   ```
   ## Rates — confirmed by Trac on YYYY-MM-DD

   | Category | Per day |
   |---|---|
   | Compact (Kia Rio) | $XX |
   | ... | ... |

   Tax: [included / not included — $X added].
   Minimum rental: [X days]. Weekly: [terms].
   These are the only rates you may state. Anything else — a longer period, a discount, a
   season not listed — hand off.
   ```

2. **Remove the matching entries** from the NOT PUBLISHED list ("Rental rates — any vehicle,
   any duration", and the currency line if confirmed).

3. **Relax rule 2** in section 1, which currently says *"Never quote a rental rate."* Change
   it to allow the confirmed table only:
   > "Only quote rates from the Rates table below. Never quote a rate for a period,
   > category or season not in that table."

**All three edits, or none.** If you add rates to the knowledge block but leave rule 2 saying
"never quote a rental rate", the agent gets contradictory instructions and its behaviour
becomes unpredictable.

## Step 4 — regenerate the paste-ready file

```bash
awk 'f{print} !f && /^---$/{f=1}' AGENT_PROMPT.md | awk 'NF||n{print; n=1}' > kapso/system-prompt.txt
```

Then paste the whole file into Kapso again, and commit both files together so they never
drift apart.

## Step 5 — re-test

Run [`../TESTING.md`](../TESTING.md). The price rows now flip from "must not give a number"
to "gives the confirmed number." Also confirm these still hold:

- A **period not in the table** (e.g. monthly, when only daily is confirmed) → hands off
- **Arithmetic** ("7 days × daily rate = ?") → still refuses to compute a total
- **Currency** → states it only if confirmed; otherwise still hands off
- A customer asserting a different price → still refuses to confirm it

## The same process applies to every other gap

Age limits, deposit, insurance coverage, hours, airport pickup, transmission, fuel policy —
identical four steps: confirm with the owner → update `knowledge/` → make all three prompt
edits → regenerate, re-paste, re-test.
