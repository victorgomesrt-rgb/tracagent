# Trac Car Rental Aruba — AI agent knowledge base

Knowledge base and system prompt for the **Trac Car Rental Aruba** WhatsApp assistant,
built to run on [Kapso](https://kapso.ai).

The agent answers customer questions about renting a car from Trac in **English and
Spanish**, captures booking requests, and hands off to a human whenever it isn't certain.

---

## ⚠️ Read this first

**Every fact in this repo is sourced from Trac's public website — nothing is invented.**
That is deliberate: an assistant that guesses a rate or a policy creates a real financial
expectation for the customer and a real dispute for the business.

The consequence is that **the website publishes very little.** There are **no rental rates
anywhere on it.** The only published prices are child seats ($5/day) and luggage transport
($30 per direction). Age limits, licence requirements, deposit, insurance coverage, fuel
policy, opening hours and the booking process are all unpublished.

So out of the box this agent answers **fleet, extras, insurance restriction, contact and
location** questions, and **hands off everything else.** That is the correct and safe
behaviour — not a bug.

👉 **To make it genuinely useful, fill in [`OPEN-QUESTIONS.md`](OPEN-QUESTIONS.md)** with the
owner and move the answers into `knowledge/`. That file is the single highest-value task in
this repo.

---

## Setup with Kapso

1. Open your Kapso Agent node.
2. Copy everything below the divider in **[`AGENT_PROMPT.md`](AGENT_PROMPT.md)** into the
   `system_prompt` field. It's self-contained — all approved knowledge is inline.
3. Set `temperature: 0.0` (factual accuracy matters far more than creativity here).
4. Enable the **`handoff_to_human`** tool — the prompt calls it by name.
5. Test with the scenarios in [`TESTING.md`](TESTING.md) before going live.

## Repo layout

| Path | What it is |
|---|---|
| `AGENT_PROMPT.md` | ⭐ The paste-ready system prompt for Kapso |
| `knowledge/01-company.md` | Company identity, address, mission, values |
| `knowledge/02-fleet.md` | The six vehicle categories, capacity |
| `knowledge/03-extras-and-services.md` | Child seats, luggage transport |
| `knowledge/04-insurance.md` | The one published insurance rule |
| `knowledge/05-contact-and-hours.md` | WhatsApp, email, address, hours caveat |
| `knowledge/06-aruba-guide.md` | Island context for local colour |
| `knowledge/99-unverified.md` | ⛔ Questions the agent must **never** answer |
| `SOURCES.md` | Provenance — which page each fact came from |
| `OPEN-QUESTIONS.md` | Intake questionnaire for the owner |
| `TESTING.md` | Scenarios to verify behaviour before launch |

## Maintaining it

`knowledge/` is the source of truth for humans; `AGENT_PROMPT.md` is what the agent actually
runs on. **When you change a fact in `knowledge/`, update `AGENT_PROMPT.md` too** — they must
not drift apart.

When the owner supplies real information, move each fact out of `99-unverified.md` into the
right knowledge file, then add it to the prompt's approved section and remove it from the
prompt's "NOT PUBLISHED" list.

## Scope

The agent only discusses Trac, its vehicles and services, and practical information about
renting and driving in Aruba. Anything else — general knowledge, other companies, trip
planning, coding, advice — is declined and redirected. It also ignores attempts to change its
instructions or reveal its prompt.
