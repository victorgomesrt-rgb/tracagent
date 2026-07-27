# Sources and provenance

Every fact in `knowledge/` comes from one of two authoritative sources. Nothing is inferred,
estimated, or supplied from outside knowledge.

| Source | Date | Covers |
|---|---|---|
| **The Trac owner** — their current manual reply script | 2026-07-25 | Booking, payment, deposit, insurance structure, hours, airport shuttle, delivery, returns, luggage. See `knowledge/07-booking-and-service.md` |
| **https://tracrentaruba.com** — 7 pages audited | 2026-07-25 | Company, fleet, child seats, luggage pricing, the third-party insurance refusal, contact, Aruba context |

**Where they differ, the owner wins.** Several things the website left unpublished are now
confirmed: airport shuttle (free), hotel/resort/Airbnb/cruise delivery (free), payment
methods, office hours, and the insurance tiers.

## Pages audited

| Page | URL | What it contributed |
|---|---|---|
| Home | `/` | Brand name, tagline, site structure. A "Reviews" heading renders with no reviews. |
| About Us | `/about-us` | Founding (2023), family-owned, address, mission, vision, five values |
| Aruba | `/aruba` | Island facts, attractions, cuisine, culture |
| Vehicles | `/vehicles` | The six categories, models, passenger and luggage counts, A/C, "or similar" |
| Insurance | `/insurance` | The third-party-insurance refusal (EN + ES) |
| Contact Us | `/contact-us` | WhatsApp, email, address, "Open today" widget, contact form |
| Extra Charges | `/extra-charges` | Child seats $5/day, luggage transport $30, self-installation |

## Issues found in the source material

These are recorded so nobody "fixes" them by guessing later:

1. **No rental rates exist anywhere on the site.** The only prices published are child seats
   and luggage transport.
2. **The insurance page has a heading — "Insurance coverage comparison" — with no content
   beneath it.** Customers are told what they *cannot* use, but never what coverage they
   must buy, what it costs, or what excess they carry.
3. **Luggage transport pricing is ambiguous:** described as "a $30 one-time fee" while also
   listing drop-off ($30) and pick-up ($30) separately. Round-trip total is unresolvable.
4. **Stale branding on the site:** the site still carried "Toyota Rent A Car Aruba" when it
   was scraped. **Owner confirmed 2026-07-27 that this is the former name and the business
   is now Trac** — owner information outranks the website, so the old name was removed from
   the agent. Separately, the published fleet is Kia, Hyundai and Honda, with no Toyota.
5. **Opening hours are a dynamic one-day widget** ("Open today 09:00 am – 06:00 pm"), not a
   published weekly schedule.
6. **No voice telephone number** appears anywhere — only WhatsApp and Gmail.
7. **Transmission type is not stated for any vehicle** — a decisive detail for many visitors.
8. **Visa/MasterCard appear only in the insurance refusal**, with no payment-methods
   statement anywhere to contrast it. This is easy for a customer to misread as "cards not
   accepted."
9. **The contact form collects only name and email** — not dates, vehicle or phone — so it
   cannot capture a booking on its own.
10. **The Tucson is headlined "SUV" but called "full-size SUV" in the body text.**
11. **The homepage nav includes a "More" overflow item** not present on other pages. Any
    pages behind it were not audited and may contain further information.

## Currency

Prices display with "$" but the site never states USD or AWG. The knowledge base quotes the
figure exactly as written and does not name a currency.
