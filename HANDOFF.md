# Project handoff / current state

**Last updated: 2026-07-25**

Read this first. It states what the project actually is, so nothing gets rebuilt on a
retired path.

---

## The stack — this is the whole list

| Layer | What |
|---|---|
| **WhatsApp + AI agent** | **Kapso** — Agent node, `system_prompt` pasted from `kapso/system-prompt.txt` |
| **Model** | **Claude Sonnet 5** (via Kapso's OpenRouter selection) |
| **Client-facing number** | **+1 202-894-7105** ("Trac Car Rental" in Kapso) |
| **Knowledge base** | This repo. `knowledge/*.md` for humans, `kapso/system-prompt.txt` for the agent |
| **Staff command center** | **Lovable** (frontend) + **own Supabase project** (auth, RLS, Vault, edge functions, storage). Lives in **`~/command-center`**, its own repo — security floor written, Supabase project not yet created |

### ⛔ Not used. Do not reintroduce.

These were explored earlier and dropped. If you see them referenced anywhere, it's stale:

- **Railway** — no longer part of this project
- **Twilio** — no longer part of this project
- **Photon / Spectrum** (iMessage) — abandoned; Kapso replaced it
- **Hermes Agent** — evaluated as the brain, never adopted

Two local directories are leftovers from that exploration and are **not** part of this
project: `~/PlatoAgent` (Bun/Twilio bot) and `~/trac-handoff` (Railway webhook service, never
deployed). Ignore both.

**`~/command-center` is different — it is current.** That's the agency platform (Lovable +
own Supabase), kept out of this repo on purpose: this one is Trac's deliverable and may be
handed over or cloned per client, so the platform's security code does not belong in it.

---

## What is live right now

The Kapso agent answers customer WhatsApp messages in **English and Spanish**, and:

- Answers from the fleet, extras, insurance, booking/payment, hours, airport and delivery
  knowledge below
- Says "the team will confirm" for anything unpublished, **and keeps helping** (does *not*
  call `handoff_to_human` for a missing fact)
- Calls `handoff_to_human` only for real escalations: asks for a person, complaint, accident
  or breakdown, booking change, abuse, or a completed booking request
- Declines anything off-topic without paging a human
- Writes without em dashes or AI tells (verified against Sonnet 5)

Kapso settings: `temperature` must be **1.0 or unset** — Sonnet 5 rejects non-default
sampling parameters with a 400. `handoff_to_human` must be enabled.

## Knowledge state

**Confirmed and answerable** (from the website plus the owner's manual reply script):
company/fleet/capacity · child seats $5/day · luggage transport $30 per direction · booking
free on the website · payment on arrival · cash/Visa/Mastercard with 4% card surcharge · no
AMEX or Discover · deposit required on standard rentals, none on Full Risk · Third Party
Liability included · CDW+ and Full Risk optional · Full Risk mandatory for 1–3 day rentals and
airport self-return before hours · hours 09:00–18:00 · $50 out-of-hours fee · office 2 min
from AUA · free airport shuttle · flight tracking · instructions emailed the day before ·
free delivery/pick-up at hotels, resorts, Airbnb, cruise terminal · free shuttle after return

**Provenance:** owner information outranks the website where they differ. See `SOURCES.md`.

## 🛑 The one blocker: rates

The owner gave, under "**daily** rate", in USD: Rio **280** · K3/Sonet/Tucson/BRV **320** ·
Staria **800**.

**These are NOT in the agent, deliberately.** $280/day for a Kia Rio is far above the Aruba
market (~$35–60/day). Read as **weekly** they line up almost exactly (280/7 = $40/day). We
cannot tell which, and the asymmetry is brutal: quote $280/day when it's weekly and you lose
every booking; quote $40/day when it's really $280/day and every rental becomes a counter
dispute.

**Resolve with one question:** *"For a Kia Rio, what does a customer pay for 1 day, and what
for 7 days?"* Then follow `kapso/ADDING-RATES.md` — it requires **three** prompt edits
together (add the rates, remove the NOT PUBLISHED entry, **and** relax absolute rule 2 which
currently says "never quote a rental rate"). Miss the third and the agent gets contradictory
instructions.

Also unresolved: whether 7% tax is added or included; whether "foreign licence accepted" and
"IDP required" (one "yes" answered two questions); and a languages contradiction — the form
says Dutch + Portuguese, the owner's script says English and Spanish only. The agent is
EN/ES per the script.

**Everything outstanding is in `QUESTIONS-FOR-OWNER.md` / `.docx`** (owner-facing) and
`OPEN-QUESTIONS.md` (working notes).

---

## Command center — moved to `~/command-center`

A multi-tenant agency app: the operator provisions clients, each client logs in and sees only
their own inbox, files, quote calculator, calendar.

**It now has its own repo at `~/command-center`.** Start there, not here — its README has the
runbook and `LOVABLE-PROMPT.md` has the standing rules plus the build prompts in order. What
follows is kept only so the reasoning stays with the rest of the project history.

The decisions that matter:

1. **Use your own Supabase project, not Lovable Cloud.** Lovable auto-provisions Cloud the
   moment a prompt needs a backend, so connect your own project *first*. Leaving Cloud later
   is possible but manual (export → remove Cloud → reconnect → rebuild schema → move data),
   and the reverse isn't supported. You want `psql`, `pg_policies`, role impersonation for
   RLS tests, your own billing and PITR when you hold several companies' customer
   conversations.
2. **Never store Kapso embed tokens.** Kapso mints them via
   `POST /platform/v1/inbox_embeds` with `expires_at`, `allowed_origins` and scope, returning
   the token once. So the token is disposable, not configuration — and the "protect the token
   table with RLS" problem disappears instead of being solved. Store only the client's
   **Kapso project API key** (project-scoped), in **Supabase Vault**, in a schema with no
   grants to `anon`/`authenticated`.
3. **Give the operator NO cross-tenant RLS policy.** An `is_operator()` branch is the one
   policy whose job is to cross tenants — hardest to test, easiest for AI chat to widen.
   Operator screens read health metadata only, via a privileged server-side route. This also
   becomes a promise you can put in a contract.
4. **The embed minter takes no tenant parameter** — derive tenant from the verified JWT `sub`.
   The signature is the enforcement.
5. **Own the privileged function deliberately.** Lovable's AI writes and deploys edge
   functions into your project, so keep the service-role/minting function under your control
   and treat any AI-generated change to it as a red flag. Let Lovable generate UI freely.

**Build order** (revised — Lovable builds the app; only the security floor is hand-written):
(1) ✅ security floor SQL, written · (2) create the Supabase project and apply it · (3) connect
Lovable, work through the prompts · (4) hand-write `mint-inbox-embed` yourself · (5) prove
cross-tenant isolation with two throwaway tenants **before** real client data.

**Four things to verify with curl before building on them:** does a minted embed URL opened
as a bare top-level tab fail; does `expires_at` cut off an already-open iframe; does Kapso
clamp short TTLs; can Kapso projects/API keys be created programmatically.

---

## Repo map

| Path | What |
|---|---|
| `kapso/system-prompt.txt` | ⭐ Paste this into Kapso's Agent node. Generated — do not hand-edit |
| `AGENT_PROMPT.md` | The source of the above, with setup notes. Edit here, then regenerate |
| `kapso/ADDING-RATES.md` | How to add rates (or any unpublished fact) safely |
| `knowledge/01`–`07` | Company, fleet, extras, insurance, contact/hours, Aruba, booking/service |
| `knowledge/99-unverified.md` | ⛔ The never-answer list |
| `QUESTIONS-FOR-OWNER.md` / `.docx` | Owner-facing questionnaire |
| `OPEN-QUESTIONS.md` | Working notes on what's answered vs missing |
| `SOURCES.md` | Provenance and contradictions found in the source material |
| `TESTING.md` | Behavioural, adversarial and tone tests |

**Regenerate the prompt after any `AGENT_PROMPT.md` edit:**

```bash
awk 'f{print} !f && /^---$/{f=1}' AGENT_PROMPT.md | awk 'NF||n{print; n=1}' > kapso/system-prompt.txt
```

Then re-paste into Kapso. `AGENT_PROMPT.md` and `knowledge/` must not drift apart.

## Next actions

1. Get the **1-day / 7-day Rio price** from the owner → rates live in ~5 minutes
2. Get the rest of `QUESTIONS-FOR-OWNER.docx` filled in
3. Command center: create the Supabase project and apply the floor — see `~/command-center`
