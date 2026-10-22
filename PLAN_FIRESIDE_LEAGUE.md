# Fireside League + Referral Cards + Pre-Cup Brief — Plan (not built yet)

> Status: **proposal for review**. Nothing here is implemented.
> Builds on the 100 Cups Connector PRD (card scan, voice notes, GHL sync, Task-Gate / Byron Rule).
> Note: the live app code (including the Circle) is not in this repo. Names below assume the Circle is the member directory/network view and should be mapped onto the real code before building.

---

## 1. The idea in one paragraph

Each week, mastermind members who opt in get paired. One hosts, one is the guest, and they record a short fireside chat that runs as a "100 Cups Live" episode on Jason's podcast. A season is one full rotation, so everyone hosts and everyone guests. Every member keeps a **Referral Card** on their profile (who's a great referral, what they need right now). The card feeds the host's prep, gets asked about on air, and pings the Circle when it changes. The original mastermind members are the **Founders Circle**. When new Academy cohorts graduate, being booked as a guest opposite a Founder is how they get invited into the mastermind. Every episode also gets cut into clips and posts.

---

## 2. Feedback: what makes it strong

- **The episode is the referral ask.** If the host always asks "who's a great referral for you right now?", every episode broadcasts a referral request to the audience. That's the link between the podcast and the Circle, and it's the strongest part of the idea.
- **Pairing forces relationships the group wouldn't form on its own.** Masterminds tend to split into cliques. A rotation breaks that up.
- **Cohort on-ramp.** "Your first episode is your invitation" is a concrete ritual, which beats a vague "join the mastermind."
- **Content flywheel for Jason.** He gets a weekly episode without hosting every one himself.
- **It reuses what Connector already does.** Voice notes, Claude drafting in Jason's voice, and the Task-Gate approval flow all apply directly to episode prep and post-episode content.

## 3. What could kill it (and how to defuse each)

| Risk | Why it kills it | Defuse |
|---|---|---|
| **No-shows / flaking** | One flake leaves a hole on Jason's podcast calendar, and trust drops fast | Opt-in closes 7+ days ahead. Both people confirm within 48h or the slot goes to a **bench** pair. Track reliability quietly, and put repeat flakes on hold for a season. |
| **Uneven episode quality** | Weak member-hosted episodes hurt Jason's show | An auto-generated **host prep brief**, a fixed 5-segment format, and **Jason approves before anything publishes** (Byron Rule). Episodes that aren't approved stay internal. |
| **"Fantasy league" turns competitive** | Scoreboards ranking people damage a giving culture | Only **giving** earns points: referrals passed, intros made, episodes completed. No public rankings on downloads or popularity. Show season stats as "the Circle gave 41 referrals," not a ranked list. |
| **Stale Referral Cards** | If the cards go stale, nobody trusts them and the system dies | The "Right now" ask **expires after 30 days**. The app nudges with "Still looking for X?" and one tap renews it. Stale cards are dimmed in the Circle. |
| **Notification fatigue** | People mute it, and then updates stop mattering | Notify only when **referral fields** change, never other profile edits. Default to a **weekly digest**, with real-time as an opt-in. Collapse multiple edits into one event per member per day. |
| **Too many episodes for one approver** | ~20 Founders could mean up to 10 pairs a week if everyone opts in. Jason approving every episode, plus its clips and posts, becomes the bottleneck and the feed floods. | Cap it at **2 matchups a week** (configurable). Extra opt-ins roll to the next week first. Jason gets one **weekly approval queue** with everything for an episode bundled, not a stream of single tasks. |
| **Rights / releases** | Repurposing a guest's face and voice without a release is a liability | A one-tap recording/repurposing release is part of confirming the slot. No release means no recording. |
| **Scope explosion** | Connector is single-user (Jason) today. This needs real multi-user | It depends on the Phase 2 "Team Collaboration / multi-tenant" item. Build that first, or validate manually (Phase 0) before writing code. |
| **Building recording/editing tools** | Months of work that Riverside or Zoom plus Opus Clip already cover | **Don't build it.** Store a recording link. Claude works from the transcript only. |

**Biggest single kill test:** run it on a spreadsheet for 4 weeks first (Phase 0). If people don't show up for a manual version, the software won't fix that.

---

## 4. The Referral Card (profile section)

Fields, all editable by the member:

| Field | Prompt shown | Notes |
|---|---|---|
| `ideal_referral` | "My best referral is…" | Who, specifically: role, industry, size, location |
| `trigger_phrases` | "Introduce me when you hear someone say…" | Up to 3 short phrases. In practice the most useful field. |
| `not_a_fit` | "Please don't send me…" | Saves everyone time |
| `right_now_ask` | "This month I'm looking for…" | **Expires after 30 days.** One concrete ask. |
| `intro_method` | "Best way to introduce me" | Email intro / text / LinkedIn / book my calendar (link) |
| `referral_gift` | "What I offer people you send me" | Optional (free audit, first cup on me, etc.) |
| `recent_win` | "A referral that worked" | Social proof, and gets used as an on-air story prompt |

Also:
- **Voice-fill:** members can talk the card out loud. Reuse the existing Whisper → Claude pipeline to fill in the fields, then show them for confirmation before saving (same "Looks good" pattern as the card scan).
- **Card history:** keep every version, so updates can be shown as a diff ("Sarah changed her ask: *commercial roofers in Nashville* → *HOA property managers*").
- **Circle view:** filter/search by trigger phrase or ask. "Who in the Circle wants intros to CPAs?"

### Update notifications

- An event fires only when a referral field changes, not on other profile edits.
- Delivery: in-app Circle feed (always) plus a **weekly digest** email (default). A real-time push/email is optional per member.
- Multiple edits from the same member in one day merge into one event.
- System notifications to members aren't follow-ups sent on someone's behalf, so they don't need the Task-Gate. Any **outbound intro** a member drafts from a card still goes through the Task-Gate.

### Referral Ledger (closes the loop)

- "I referred someone to Sarah" is logged in one tap from her card: `from`, `to`, `referred_contact` (optional, can link a Connector contact), `status` (sent / met / closed / thanked).
- The receiver gets a prompt to thank the sender, drafted in their voice and gated by the Task-Gate.
- This ledger is the only source of league points.

---

## 5. The Fireside League

### Weekly flow

1. **Mon:** "Opt in for next week?" One tap. (Can also opt in for the whole season.)
2. **Wed:** Opt-in closes. The pairing engine proposes matchups. **Jason reviews and can override.** Pairings publish.
3. **Within 48h:** Both people confirm and sign the release. The guest picks a slot from the host's calendar link. Unconfirmed slots go to the bench.
4. **3 days before:** The host gets a **Prep Brief** (Claude-generated from the guest's Referral Card, profile, past episodes, and Connector notes): 5 questions, the guest's "right now" ask, and one recent win to bring up.
5. **Record** (Riverside/Zoom, outside the app). The host pastes the recording link.
6. **After:** The transcript gets uploaded or pulled in, and Claude drafts **episode assets as Task-Gate tasks**: title, show notes, 3–5 clip timestamps with hook lines, a 100 Cups social post for each person, and a thank-you note. Jason approves the bundle (episode, clips, posts) in one pass before anything is published.

### Episode format (fixed, keeps quality consistent)

1. Origin story (5 min)
2. What you're building right now (5 min)
3. A referral that changed your business (5 min)
4. **"Who's a great referral for you right now?"** (required, read from the Card)
5. Rapid fire / Bonnaroo question (2 min)

### Pairing rules (in priority order)

1. Never repeat a pairing within a season.
2. Alternate roles. Whoever hosted last time is a guest next time.
3. Prefer **complementary** referral cards (A's ideal referral overlaps with B's audience) over identical industries.
4. When a cohort is joining, pair **Founder host × Academy guest** first.
5. Odd number of opt-ins: the leftover person is the bench pair's first pick, or Jason hosts them.

A greedy scorer is enough to start. No optimizer needed for groups under 50.

### Seasons & "fantasy" layer

- **Season** = everyone hosts once and is a guest once. With ~20 Founders that's 20 episodes, so at 2 a week a season runs **~10 weeks** (about 4 seasons a year, leaving room for breaks). With 190 possible pairs, nobody needs to repeat a pairing for years. It ends with a recap episode hosted by Jason.
- **Points (giving only):** episode completed +3, referral passed +2, referral closed +5, thank-you sent +1.
- **Season awards** instead of leaderboards: "Most Generous," "Best Connector," "Founders Circle MVP." These make good content too.
- **Founders Circle badge** on original members' profiles. Academy grads earn a "Season X Guest" badge on their first episode, and that badge is their invitation into the mastermind.

---

## 6. Pre-Cup Brief (relationship prep)

Before any 100 Cups conversation, the app produces a 2-minute brief on the person you're meeting, plus a drafted intro email. It came from a ChatGPT run on Michael Faas (Echo Cyber): the content was useful, but at ~1,500 words it was too long to read before coffee, the email missed the 100 Cups connection, and it broke the brand voice rules.

### Inputs

- **Them:** a business card scan (existing Card Scan), a pasted email signature, a LinkedIn URL and/or a website, plus optional "how we met" notes.
- **Me:** the signed-in member's own **Referral Card**. This replaces any hardcoded "my businesses" paragraph, so the brief works for every member. It's also what makes the engine portable later (see "Path to a Zenoflo product" below).
- **Their Referral Card, if they're in the Circle.** Then the referral sections come from what both people wrote themselves, and web research only fills in background. Web research is the fallback for people outside the Circle, not the main source.

### Two layers: the rules in the background, the short brief in front

The long business-development prompt is worth keeping for its quality control, not its length. Its rules run as the **system prompt** on every brief. The member only sees the short output.

**System rules (always on):**
- Use only verifiable public facts, and cite a source for each one.
- Label facts and hypotheses separately. Partnership ideas are always hypotheses.
- Flag anything conflicting or out of date (rebrands, redirects, old pricing). *Example: echocyber.io redirecting to nightglassadvisory.com.*
- Don't confuse people with similar names. If the match is uncertain, say so.
- Never invent history, customer lists, prices or credentials. Never claim a prior relationship unless the member provided one.
- Challenge weak fits instead of forcing one, and name one idea to skip.
- Don't propose referral fees or commissions in a first email.
- All generated copy follows `BRAND_VOICE.md`.

**Brief template (what the member reads, under 300 words):**

```
You're prepping me for a 100 Cups conversation with {name}. I'll read this on my phone in 2 minutes. Be brief.

THEM: {card fields, website, LinkedIn, how we met, their Referral Card if they have one}
ME: {my Referral Card: what I do, my ideal referral, what I'm looking for right now}

RESEARCH
Check their website and LinkedIn. Use only what you can verify and cite each source. Flag anything that conflicts or looks out of date (rebrand, redirect, old pricing).

BRIEF (under 300 words)
1. Who they are: 3 bullets, verified facts only.
2. What they sell, and to whom: one line each.
3. Send them: 2 situations where I should refer someone to them.
4. They send me: 2 situations where they'd refer someone to me.
5. Best fit: the single strongest way we could work together, labeled as a hypothesis. Name one weak idea to skip.
6. Ask over coffee: 3 open questions that get them talking.
7. Verify: anything I should confirm in person.

EMAIL
Under 150 words, peer to peer. Mention how we're connected (100 Cups) if true. Use one specific, verified detail. Never invent history. End by asking for a 30-minute coffee. Follow BRAND_VOICE.md. Return it as a draft. Never send.
```

### How it fits the rest of the app

- **Listen first.** In Connector it's framed as relationship prep, not a sales assistant. The brief leads with questions to ask, to support the 70% listening goal.
- **Email goes through the Task-Gate.** The draft becomes a pending `email` task. Nothing sends without approval (Byron Rule).
- **Closes the loop after the cup.** The existing voice note updates the contact's "what they're looking for right now", which feeds the 30-day follow-up. Any referral that comes out of it gets logged in the Referral Ledger.
- **Dated and sourced.** Each brief stores when it was researched and its sources. Briefs older than ~90 days get re-checked before the next cup.
- **Brief about yourself.** Optionally send your own Referral Card to the other person ahead of the cup, so they show up prepared too.
- **One engine, three uses:** Pre-Cup Brief, the Fireside League host prep sheet (section 5), and an onboarding brief when an Academy graduate joins.

### Path to a Zenoflo product ("Relationship Intelligence")

Paste a card, signature or LinkedIn URL and get a research brief, reciprocal referral ideas and drafted outreach. That's Connector's existing pipeline plus one research step, so:

1. **Build it in Connector first.** The Circle, the Referral Cards and ~20 weekly users give real feedback.
2. **Keep "ME" swappable from day one.** Business context always comes from the signed-in user's Referral Card, never hardcoded.
3. **Then the Zenoflo version is a re-skin, not a rebuild.** It's the same engine, using the multi-tenant/white-label items already in PHASE_2_NOTES. Outside the Circle it relies only on web research, and the "sales assistant" framing is fine there.

### Validation before building

The 96/100 was ChatGPT grading its own prompt. Instead, run the long and short versions on 5 people Jeremy already knows well (Michael included). Score each on accuracy, and on whether Jeremy would send the email with light edits. Ship whichever wins.

---

## 7. Data model sketch (Drizzle, additive)

```ts
members            { id, clerk_user_id, name, photo_url, is_founder, cohort, joined_at, reliability_score }
referral_cards     { member_id, ideal_referral, trigger_phrases[], not_a_fit, right_now_ask,
                     right_now_expires_at, intro_method, referral_gift, recent_win, updated_at }
referral_card_versions { id, member_id, snapshot_json, changed_fields[], created_at }   // powers diffs + notifications
circle_events      { id, member_id, type ('card_updated'|'episode_published'|'referral_closed'), payload_json, created_at }
notification_prefs { member_id, digest ('weekly'|'off'), realtime (bool) }
seasons            { id, name, starts_on, ends_on, status }
season_weeks       { id, season_id, week_of, optin_closes_at }
optins             { id, week_id, member_id, created_at }
matchups           { id, week_id, host_id, guest_id, status ('proposed'|'confirmed'|'recorded'|'approved'|'published'|'cancelled'),
                     host_confirmed_at, guest_confirmed_at, release_signed_at, scheduled_for, recording_url, transcript }
episode_assets     -> reuse existing `tasks` table with new types: 'show_notes' | 'clip' | 'episode_post' | 'thank_you'
referrals          { id, from_member_id, to_member_id, contact_id (nullable), status, points_awarded, created_at }
briefs             { id, owner_member_id, contact_id (nullable), subject_member_id (nullable), purpose ('pre_cup'|'host_prep'|'onboarding'),
                     inputs_json, body_md, facts_json, hypotheses_json, flags_json, sources[], researched_at }   // email draft -> `tasks` row
```

New routes: `/circle` (feed + cards), `/me/card`, `/league` (this week, my matchup, season), `/league/admin` (pairing review), `/episodes/:id`, `/contacts/:id/brief`.

---

## 8. Phased rollout

| Phase | What | Exit criteria |
|---|---|---|
| **0. Manual pilot (4 weeks, no code)** | Google Form for opt-ins and Referral Cards. Jason pairs by hand. Claude Project generates prep briefs. | ≥80% of pairings record. Members say the cards were useful. |
| **1. Multi-user foundation** | Clerk orgs/members, roles (admin, founder, member, guest). This is the Phase 2 multi-tenant item from PHASE_2_NOTES. | Members can log in and see the Circle. |
| **2. Referral Cards + notifications** | Card editor (typed and voice-fill), version history, Circle feed, weekly digest, 30-day expiry nudges, Referral Ledger. | Worth having even if the league never ships. |
| **2b. Pre-Cup Brief** | Brief engine (system rules + short template), research step, uses both Referral Cards when available, email draft into Task-Gate, ~90-day refresh. Run the 5-contact validation first. | Jeremy would send the email with light edits for 4 of 5 test contacts. |
| **3. League engine** | Opt-in, pairing proposal + admin override, confirmations, release, bench, prep brief. | A full season runs in-app. |
| **4. Episode content pipeline** | Recording link + transcript in, Claude drafts assets as gated tasks, weekly approval queue for Jason. | Episode-to-published-clips turnaround under 48h. |
| **5. Cohort on-ramp** | Academy guest invites, Founder × Academy pairing priority, badges, season awards/recap. | First Academy grad converts into the mastermind. |

Phase 2 is valuable on its own, which lowers the risk. If the league fizzles, the Referral Cards still pay off.

## 9. Tests to add (in the PRD's style)

- Byron Rule: no episode asset or intro is published or sent without an approved task.
- Editing a non-referral field doesn't create a `card_updated` event. Editing a referral field creates exactly one event per day.
- An expired `right_now_ask` is dimmed and triggers a nudge.
- The pairing engine never repeats a pairing within a season and alternates roles.
- A matchup can't move to `recorded` without `release_signed_at`.
- A brief never creates a sent email. The draft lands as a `pending` task.
- When both people have Referral Cards, sections 3 and 4 of the brief cite the cards, not web sources.
- Every fact in a brief has a source. Partnership ideas are labeled as hypotheses.

## 10. Open questions for Jeremy / Jason

1. What exactly is "the Circle" in the current app (screen/table names)? This plan needs to be mapped onto it.
2. ~~Founders headcount~~ About 20. Still open: how many per Academy cohort?
3. ~~Approver~~ Jason approves every episode.
4. Recording tool (Riverside, Zoom, StreamYard)? Clip tool (Opus Clip, Descript)?
5. Is 2 member episodes a week the right cap for the feed, alongside Jason's own interviews?
6. Should Academy guests see the Circle's Referral Cards before they're members, or only after?
7. Pre-Cup Brief: should research run automatically on card scan, or only when someone taps "Prep me"? (Cost vs. convenience.)
