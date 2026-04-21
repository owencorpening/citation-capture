# Citation Capture

**Every citation error is a write-off. Every missed source is a risk. This tool eliminates both.**

Citation Capture is a one-click browser tool that logs legal citations to a private Google Sheet at the moment of research — with zero friction, zero infrastructure cost, and zero data leaving your control.

Built on the same architecture used by journalists and content creators for attribution tracking, adapted for the specific demands of legal practice.

---

## The Problem It Solves

Legal research is non-linear. Attorneys and clerks jump between Westlaw, Fastcase, CourtListener, Google Scholar, and slip opinions — often across multiple matters in a single session. Citations get copied into scratch documents, sticky notes, browser tabs left open "just in case."

Then the brief is due.

- **Time lost reconstructing sources** that were found but not logged
- **Write-offs** when you can't bill for time you can't document
- **Citation errors** from transcription — wrong reporter, wrong page, wrong year
- **Malpractice exposure** from missed controlling authority that was found but lost in the shuffle

Citation Capture closes the loop at the moment of discovery, before anything gets lost.

---

## What It Does

A single click on any legal research page:

1. **Captures** case name, citation, court, date, and source URL from the page
2. **Logs** a formatted Bluebook citation to your private Google Sheet
3. **Tags** the citation with the current matter and document section you're working on
4. **Confirms** with a one-line alert — then you're back to work

The entire interaction takes under two seconds.

---

## Why This Architecture

Citation Capture runs on **Google Apps Script** — Google's free serverless platform. This means:

- **Zero infrastructure cost** — Google hosts your endpoint, permanently, for free
- **Your data stays private** — logs write to your own Google Sheet, not a third-party database
- **No login, no subscription, no vendor** — you own the entire stack
- **Each attorney gets their own instance** — complete data isolation by design

This is not a SaaS product. It is a personal tool that each user deploys once and owns permanently.

---

## Matter Tagging

Before starting research on a new matter, run one command:

```bash
setmatter "Smith v. Jones" "Argument II.A"
```

Every citation logged until the next `setmatter` call is automatically tagged with that matter and section. Switch matters in two seconds, no context lost.

The current matter is served locally so the bookmarklet can read it without any cloud dependency.

---

## What Gets Logged

| Column | Content |
| ------ | ------- |
| A | Timestamp |
| B | Case name |
| C | Citation (Bluebook formatted) |
| D | Court |
| E | Date decided |
| F | Matter / document section |
| G | Source URL |
| H | Full Bluebook string |

---

## Source Coverage

| Source | Case name | Citation | Court | Date |
| ------ | --------- | -------- | ----- | ---- |
| Google Scholar | ✅ | ✅ | ✅ | ✅ |
| CourtListener | ✅ | ✅ | ✅ | ✅ |
| Fastcase | ✅ | partial | ✅ | ✅ |
| Westlaw | ✅ | — | ✅ | ✅ |
| Lexis | ✅ | — | ✅ | ✅ |
| Slip opinion (PDF) | ✅ | — | ✅ | — |

Westlaw and Lexis use obfuscated markup — case name and court are reliably captured; citation string requires manual confirmation. Google Scholar and CourtListener are fully automated.

---

## The ROI Case

A mid-size litigation firm with 10 associates averaging 2 hours per week on citation reconstruction and error correction:

- **20 hours/week** of non-billable or written-off time
- **At \$300/hr blended rate: \$6,000/week, \$312,000/year**
- **Setup time per attorney: 20 minutes**
- **Cost: $0**

Citation errors that reach a filed brief carry additional risk that is harder to quantify and easier to avoid.

---

## Status

Architecture proven. Core bookmarklet and logging pipeline complete (adapted from production image attribution tooling). Legal-specific DOM scrapers and Bluebook formatting logic in development.

**If you are interested in early access or want to shape the feature roadmap, open an issue or reach out directly.**

---

## Technical Overview

```text
[Browser Bookmarklet]
       ↓ (one click on any case page)
[Scrapes citation metadata from DOM]
       ↓ (GET request)
[Google Apps Script endpoint — free, hosted by Google]
       ↓ (formats Bluebook citation)
[Your Google Sheet — citation logged]
```

```text
[setmatter "Smith v. Jones" "Arg II.A"]
       ↓
[~/.citation-matter — plain text]
       ↓
[Local title server on port 9876]
       ↓
[Bookmarklet reads current matter on each click]
```

---

## Deployment

Each user deploys their own instance. Setup takes approximately 20 minutes:

1. Copy `Code.gs` into a new Google Apps Script project
2. Add API credentials to Script Properties
3. Deploy as a Web App
4. Install the bookmarklet
5. Run `setmatter` before each research session

Full setup instructions in [SETUP.md](SETUP.md) *(coming soon)*.

---

*Built on proven zero-infrastructure architecture. Your data. Your sheet. Your tool.*
