---
layout: court-call
permalink: /court-call/privacy/
title: "Court Call — Privacy Notice"
description: "How the Court Call tennis community handles your data: minimal, self-hosted, never sold."
---

> ⚠️ **Not legal advice.** This is a plain-language notice written to be honest and accurate about how
> Court Call handles your data. It is **not** a lawyer-reviewed legal document; treat it as a
> good-faith summary rather than binding legal terms.

**Last updated:** 2026-05-31 · **Version:** v1.0

---

## The short version

Court Call is a small, player-run, self-hosted tennis community on Discord. We collect as little
as possible. We don't sell your data, we don't run ads, and we don't track you across the web.
Our own systems store two kinds of things: (1) the **profile details you choose to share** so the
bot can match you with players (like your availability), and (2) minimal **records that you confirmed
you're 18+, agreed to the Guidelines, plus a short log of account events** so we can run the place
and debug problems. You control your profile — remove a detail and it's gone from our server. Delete
your account and your profile is erased; we keep only a minimal record that the account existed, for
up to 24 months, then that's deleted too. Everything else is your normal Discord activity, governed
by Discord.

---

## 1. Who runs Court Call

Court Call is operated by an individual volunteer — not a company, club, or venue. Contact for any
privacy question or request: **Discord @aaas24**.

---

## 2. The two layers: Discord vs. our systems

It helps to separate two things:

- **Discord** is the platform we run on. When you use Discord, your account, messages, profile,
  and activity are handled by Discord under **their** privacy policy and terms — not ours. We don't
  control that, and you should read Discord's policy directly: https://discord.com/privacy
- **Court Call's own systems** (our self-hosted bot and database) store only the minimal records
  described below. This notice covers *our* systems.

---

## 3. What we collect (and what we don't)

### We store, in our own self-hosted database:

**Profile data** — details you choose to share so the bot can help match you with players:
- Information you add to your Court Call profile, such as your **gender**, **availability windows**,
  and anything else shown in your profile.
- This is **opt-in and self-managed**: you decide what to put there, and you can change or remove
  any field at any time. We keep no history of profile changes.

**Verification record** — when you pass the join gate:
- Your Discord user ID (a numeric account identifier)
- That you confirmed you are 18 or older (a yes/no — see below)
- That you agreed to the Community Guidelines, and which version
- Your current status (active / hold)
- A timestamp

**Audit log** — a minimal record of account lifecycle events for safe operation and debugging:
- Your Discord user ID
- The event (e.g. joined, verified, reminded, removed, rejoined, guidelines re-confirmed)
- A short reason (e.g. "did not verify within 7 days")
- A timestamp
- *(The audit log records that an account existed and what happened to it — not your profile content.)*

### We do NOT collect or store:
- **Your date of birth, birth year, or age.** The 18+ gate stores only the *yes/no result* of your
  confirmation — never your actual age or birthdate.
- ID documents, face scans, or any third-party age-verification data. We don't use those services.
- Your real name, address, phone number, email, or payment info. (There are no payments — Court Call is free.)
- **Cross-site advertising / ad-tracking identifiers.** (Our website does use cookies for basic
  analytics — see §5 — but not for advertising, and we don't track you across other sites.)
- Any profile detail you haven't chosen to add yourself.

> **Important honesty note:** confirming you're 18+ is *self-attested*. We **require** members to be
> 18 or older and remove under-18 accounts when we become aware of them, but we do **not** verify
> ages and don't claim to.

---

## 4. Why we collect it (purpose)

- **Profile data:** so the bot can do its core job — help you find and match with other players
  (e.g. surfacing people whose availability overlaps yours). You provide it because it makes the
  matching useful; it's yours to manage.
- **18+ confirmation:** to enforce our adults-only rule. Court Call connects people who may meet in
  person; as a small volunteer community we can't provide the safeguards a space with minors needs.
- **Guidelines agreement + version:** to record that you accepted the rules, and to ask you to
  re-agree if the rules change.
- **Audit log:** to operate the community responsibly — investigate problems, recognize accounts
  removed for cause that try to rejoin, and debug our own bot. It records that an account existed
  and its lifecycle — not your profile content.

---

## 5. The web hub (web.aaas24.io/court-call)

Our landing page and these docs are static pages. The page uses **Google Analytics** (loaded through
Google Tag Manager) to see basic, aggregate visit data — roughly how many people land on the page and
where from — so we can tell whether our outreach is working. These are third-party services (Google)
and set cookies in your browser. To be straight with you:

- **No selling data.** We don't sell or trade anything the analytics collect.
- **No ads.** We don't run advertising or ad-retargeting networks.
- **Aggregate only.** We look at visit counts, not an individual profile of you.

One honest distinction: the **community's own systems** — the bot and database that hold your
Court Call profile — are self-hosted on infrastructure we control (see §8). The **website's visit
analytics** are the one place we lean on a third party (Google), and only for aggregate page stats.

The page links out to Discord; once you click through, Discord's policy applies. The page does **not**
read a live Discord member count, so it makes no background calls to Discord on your behalf.

---

## 6. How long we keep it (retention)

- **Profile data:** kept only while you choose to keep it. Remove a field and it's deleted from our
  server immediately, with no copy retained. When you delete your account, **all your profile data
  is erased** — we do not keep it.
- **Verification record:** kept while it reflects your current standing. When you leave or are
  removed, the active record is retired and a minimal event is written to the audit log.
- **Audit log:** the bare fact that an account existed and its lifecycle events — kept for **24 months**
  from each event, then **permanently and fully deleted**. We do not keep an anonymized copy, and the
  audit log never contains your profile content.

So after you delete your account: your profile is gone right away; only a minimal "this account
existed / these events happened" record remains, and that too is fully deleted within 24 months.

*(Retention principle: we keep data only as long as needed for the purpose above. The 24-month
period is our operating default and is one of the items to confirm under legal review.)*

---

## 7. Who we share it with

- **No one for marketing.** We never sell, rent, or trade your data, and we don't run ads.
- **Service providers we run on:** Discord (the platform) and our own self-hosted infrastructure.
  We don't hand your records to third-party data processors beyond what's needed to run the bot.
- **Legal:** we would only disclose information if required by a valid legal obligation.

---

## 8. Where your data lives (self-hosted)

Court Call's records live on infrastructure the operator controls directly — not on a third-party
platform that monetizes activity. This is a deliberate, data-ownership-first choice.

---

## 9. Your choices and rights

- **Control your profile directly.** You decide what profile details to share, and you can edit or
  remove any of them at any time. Removing a detail deletes it from our server immediately.
- **Leave anytime.** Leaving the Discord server / deleting your account erases your profile data and
  retires your active verification record (a minimal account-existed event persists per the 24-month
  rule above, then is fully deleted).
- **Ask what we hold.** You can request a copy of the records our systems hold about you.
- **Ask us to delete.** You can request deletion of your records, subject to the minimal audit
  retention we need for safety; tell us and we'll explain what we can remove and when.
- Depending on where you live, you may have additional rights (e.g. under GDPR or similar laws).
  To exercise any of these, contact **Discord @aaas24**.

---

## 10. Children

Court Call is for adults **18 and older**. It is not intended for, or directed to, anyone under 18.
We don't knowingly keep accounts for under-18 users; if we learn an account belongs to someone under
18, we remove it. If you believe an under-18 person is using Court Call, contact **Discord @aaas24**.

---

## 11. Security

We take reasonable measures to protect the limited data we hold. No system is perfectly secure, and
we can't guarantee absolute security — but by collecting so little, there is very little to expose.

---

## 12. Changes to this notice

If we change this notice, we'll update the version and date above and announce material changes in
the community. Continued use after a change means you accept the updated notice.

---

## 13. Contact

Questions or requests about your data: **Discord @aaas24**.
