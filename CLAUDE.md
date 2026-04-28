# CLAUDE.md — citation-capture

## Status
- **State:** In Progress
- **Next action:** Validate DOM scraping manually on Google Scholar and CourtListener before writing any code.
- **Last updated:** 2026-04-28

## What this is

A legal citation logging tool built on the same architecture as `~/dev/image-capture`. A browser bookmarklet scrapes citation metadata from legal research pages and logs formatted Bluebook citations to a private Google Sheet via Google Apps Script. A local matter-tagging system lets attorneys tag citations to the current brief/matter without any cloud dependency.

**Status: pitch + architecture stage. No code written yet beyond this README.**

## Sister repo

`~/dev/image-capture` is the proven implementation of this architecture — read it first. Everything here is an adaptation of that pipeline:

- Bookmarklet → Apps Script → Google Sheet is identical
- Local title server pattern (port 9876, systemd service) is identical
- `setmatter` is the same as `setimage` — writes a plain text file, served locally
- `npm run deploy` pattern for minification and service restarts should be replicated here

## What needs to be built

### 1. Bookmarklet (`bookmarklet.js`)

Same structure as image-capture bookmarklet. Key differences:

- Instead of scraping `og:image`, scrape citation metadata from the page DOM
- Instead of downloading an image, just log and confirm
- Fetch current matter from `http://localhost:9876/` (same title server pattern)
- Pass `caseName`, `citation`, `court`, `dateCided`, `postTitle` (matter), `url`, `token` to Apps Script

**DOM scraping targets by source:**

| Source | Case name selector | Citation |
| ------ | ----------------- | -------- |
| Google Scholar | `#gsl_case_name` | `#gsl_reference` |
| CourtListener | `h1#case-name` | `.citations` |
| Fastcase | `.case-name` | `.citation` |
| Westlaw | `.co_headtext` | manual fallback |
| Lexis | `.titletext` | manual fallback |

Fallback for all: `document.title` usually contains the case name.

### 2. Apps Script (`Code.gs`)

Same structure as image-capture Code.gs. Key differences:

- Accept params: `caseName`, `citation`, `court`, `dateDecided`, `matter`, `url`, `token`
- Build Bluebook string server-side: `caseName, citation (court dateDecided).`
- Append row to sheet with columns A–H as documented in README
- No external API calls needed (unlike image-capture which calls Pexels/Unsplash)

### 3. `setmatter.sh`

Same as `setimage.sh` but simpler — no series/part mapping needed. Just writes whatever the user passes to `~/.citation-matter`:

```bash
setmatter "Smith v. Jones" "Argument II.A"
# writes "Smith v. Jones — Argument II.A" to ~/.citation-matter
```

### 4. Matter title server

Identical to `image-title-server.py` in image-capture — serves `~/.citation-matter` on port 9876. Can literally be copied and pointed at the different config file.

### 5. `deploy.sh` and `package.json`

Copy from image-capture, adjust file names.

## Target users

- Associates at litigation firms doing heavy research
- Law clerks (federal and state)
- Law review editors
- Advanced law students (moot court, clinics)

## Pitch contacts

- Owen's friend (recent law grad, daily citation work pain)
- Owen's brother (senior attorney, Bates ~'83, signs the checks)

## Key selling points (from README)

- Zero cost, zero vendor, data stays private
- 20-minute setup, permanent ownership
- ROI case: 10 associates × 2hrs/week × $300/hr = $312K/year in recoverable time
- Malpractice risk reduction is the closer for senior attorneys

## What to validate before building

1. Does the DOM scraping actually work on Google Scholar and CourtListener? (Test manually first)
2. Is Bluebook formatting consistent enough to automate, or does it need per-source logic?
3. Do target users already have Google Sheets in their workflow, or is that friction?
