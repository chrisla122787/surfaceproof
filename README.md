# SurfaceProof Ledger

A before-and-after job completion report tool for pressure-washing and
exterior-cleaning businesses. Tagline: **"Finish the job. Keep the proof."**
Permanent subtitle (shown under the name in the app and on the landing page):
**"Job completion reports for exterior cleaning professionals."**

Positioning: built first for businesses that serve **commercial properties,
HOAs, apartment complexes, property managers, and absentee owners** — the
clients who can't walk the property and who ask for documentation — with
residential work second. Operators document pre-existing damage before work
begins, show exactly what was cleaned, and keep organized, dated job
documentation to help resolve customer questions and disputes.

Operators build the job they're on — each surface, the service performed,
condition notes, a photo before and a photo after, any pre-existing damage —
work a six-step job checklist, and produce a branded PDF job completion
report the client can trust, with a clear **Documentation status:
COMPLETE / INCOMPLETE** and the counts behind it (surfaces documented,
required photos, checklist %).

Pricing: **$19/month founding-member pricing** for the first 25 customers,
locked in while the subscription stays active. Standard price is **$29/month**
after the founding spots are gone. Flat price, **no per-job fees**. The job
builder (surfaces, before/after photos, checklist) is free for the job you're
working on, and every free user gets **one complete branded PDF report free**
(no credit card, no watermark — see "First report free" below). Pro unlocks
unlimited PDF reports, an evidence pack for every saved job, the job history
dashboard, and backup export/import.

Market context: WashOps charges $59/month and markets a "Transformation
Report"; CompanyCam ($63–$249/month) proves photo documentation sells.
SurfaceProof Ledger is the standalone, flat-$19 version for operators who
only need the proof, not a full operations platform.

Plain static HTML/CSS/JS — no build step, no backend.

## Files

| File | What it is |
|---|---|
| `index.html` | Landing page: promise, how it works, free/Pro features, a static sample report, founding pricing, privacy + billing/cancellation notes. |
| `app.html` | The tool: company profile, job builder (surfaces with before/after photos in IndexedDB, service + condition notes, pre-existing damage items with photos + notes, invoice/work-order/PO references), six-step job checklist, live documentation status + watermarked report preview, branded PDF report (jsPDF via CDN), evidence pack ZIP with SHA-256 checksums (JSZip via CDN), job history dashboard, backup export/import. |
| `README.md` | This file. |

## Before launch

- In `index.html`, replace the two `STRIPE_LINK` placeholder hrefs (hero +
  pricing subscribe buttons) with the real Stripe subscription payment link
  for SurfaceProof Ledger ($19/month founding price). In `app.html`, replace
  the `STRIPE_LINK` constant at the top of the script (it feeds the Pro gate's
  subscribe button).
- Contact email in the footer is `chrisla122787@gmail.com`.

## Free vs Pro

**Free (no payment, no account):**

- Full job builder for the job you're working on (one job at a time — the
  in-progress job is kept as a draft in the browser):
  - Surfaces: add any of the standard types — Driveway, House siding, Roof,
    Deck/Patio, Fence, Sidewalk, Pool deck, Commercial lot — or a custom
    name; rename, remove, and record the **service performed** and
    **condition notes** per surface.
  - **Before and after photos per surface** (stored in IndexedDB,
    downscaled to at most 1280px). Photos are deliberately *not* Pro-gated:
    the status is meaningless without them, so the free builder includes
    them. This differs from TurnoverLedger, where the checklist is the core
    free artifact and photos are the Pro layer.
  - **Pre-existing damage & exceptions**: a job-level section for damage
    that's already there before work begins — a photo set plus a note per
    item. Optional, but every item added must carry a note (see "The job
    logic").
  - Optional **reference fields** — Invoice #, Work order #, PO # — printed
    on the report whenever they're filled in (commercial clients and
    property managers run on these numbers).
  - The six-step job checklist: Arrived on site · Water access confirmed ·
    Property protected (plants, outlets, vehicles) · Work completed ·
    Final walkthrough done · Invoice sent.
  - Live summary + a report preview (watermarked "PREVIEW" for free users;
    the downloaded PDF never carries a watermark).
- **First report free:** one complete branded PDF transformation report —
  see "First report free" below.

**Pro (subscription):**

- Unlimited branded PDF job completion reports: company name/logo,
  client/address/date, technician, any reference numbers filled in, the
  documentation status + counts, per-surface service + notes with
  **before/after photos side by side**, a **Pre-existing damage &
  exceptions** section (notes + photos), the job checklist, and an optional
  **customer acknowledgment block** ("Work completed and property condition
  acknowledged" — client printed name, signature, and date lines; a
  checkbox in the app, on by default). This PDF is the client-facing
  deliverable.
- **Evidence pack (ZIP) per saved job:** the report PDF, the ORIGINAL
  before photos (`Before_Photos/`), the original after photos
  (`After_Photos/`), the original damage photos (`Pre-existing_Damage/`),
  `Job_Record.json` (the full job record), `Report_Manifest.txt` (report
  ID, client, date/time, technician, references, documentation status +
  counts, file list, and a SHA-256 hash entry per file), and
  `SHA256_Checksums.txt` (one `<hash>  <filename>` line per file in the
  pack, the checksums file itself excepted). Hashes are computed locally in
  the browser (Web Crypto) at export time. **Honest limit, stated in the
  manifest and here: SHA-256 hashes show whether exported files changed
  after export; they do not prove when a photo was taken or that the work
  was performed.** Built entirely in the browser (JSZip via CDN). Once
  exported, the saved job is flagged `evidenceExported` and the history
  list shows "Evidence pack exported ✓". The pack is the user's own
  backup — SurfaceProof Ledger never stores a copy, and the UI says so in
  plain words.
- Job history dashboard: every saved job with date, documentation status,
  and surface count; totals for jobs this month and all time; load /
  re-print / evidence-pack / delete per job. A reminder line sits at the
  top of the history card: "Export an evidence pack or backup regularly —
  your records live only in this browser."
- Backup export (JSON, photos included) / import-restore.

## First report free

Free-tier users can generate **one** complete branded PDF report (the same
report Pro users get — photos included, no watermark). It is tracked by a
localStorage counter (`surfaceproof.freereport.v1`): the first successful
PDF download consumes it, after which the PDF button asks for Pro. This is
best-effort only — there is no registration or server check, so clearing
browser data resets the counter. That is accepted at MVP: the free report
is a sampling device, not a security boundary.

## ⚠️ MVP access note (read before launch)

Pro unlocking is **client-side only** for launch validation:

- The "I already subscribed — unlock Pro" button in `app.html` simply sets a
  `localStorage` flag (`surfaceproof.pro.v1 = "1"`) in the visitor's own
  browser. There is **no real payment verification** — no server checks the
  Stripe subscription, and anyone who finds the button can unlock Pro without
  paying.
- This is a deliberate MVP trade-off to validate demand cheaply on a $0
  static stack (the landing page and the app say so in plain words: unlocking
  is on the honor system during opening validation).
- Before scaling past validation, replace this with a real entitlement check
  (e.g. a small serverless function that verifies the Stripe subscription by
  email before issuing an unlock token).

## The job logic

All core logic is in pure functions in `app.html`:

- **Surface** — `{ type, service, notes, beforeIds:[], afterIds:[] }`.
  `surfaceComplete(s)` is true when the surface has at least one before
  photo **and** at least one after photo.
- **Damage item** — `{ note, photoIds:[] }`, job-level. Photographed and
  noted BEFORE work begins. Damage items are optional — a job with none is
  fine — but an item that exists must carry a note to count as documented.
- **Job record** — `{ client, address, technician, when, invoiceRef,
  workOrderRef, poRef, surfaces:[], damage:[], includeAck, checklist,
  verdict, evidenceExported }`. The three reference fields are optional
  free text, shown on the preview/PDF/manifest only when filled.
  `includeAck` (default `true`) controls the PDF's customer acknowledgment
  block. `verdict` is the stored boolean form of the documentation status
  below.
- **Checklist** — a fixed six-step list (`CHECKLIST`); the working state
  keeps a boolean per key. `checklistDone` counts ticked steps;
  `checklistComplete` requires all six.
- **Documentation status** — `docStatus(job)` returns the status plus the
  counts (`surfacesDoc/surfacesTotal`, `photosHave/photosNeed`,
  `checklistPct`, `damageNoted/damageTotal`). The status is **COMPLETE**
  only when **all** of these hold:
  1. there is at least one surface,
  2. **every** surface is complete (≥1 before photo and ≥1 after photo),
  3. the job checklist is fully ticked (6 of 6), and
  4. every damage item (if any exist) has a note.
  `computeVerdict(job)` is kept as the boolean form (`docStatus(job)
  .complete`) for saved records. The status is always shown with its
  counts — "Surfaces documented X of Y · Required photos X of Y ·
  Checklist Z%" — in the app summary, the preview, the PDF, and the
  evidence-pack manifest. A beautiful after photo with no before photo is
  a claim, not proof — the status stays INCOMPLETE, and the summary card
  says exactly what's missing.
- **History totals** — `historyTotals(jobs, now)` counts saved jobs in the
  current calendar month and all time.
- **Photos** — attached photos are downscaled to at most 1280px (JPEG, 0.82
  quality) where the browser supports canvas, then stored in IndexedDB under
  a generated id. Job records store only the photo ids. (The evidence pack
  exports the stored originals — i.e. the full-resolution version the app
  holds, which is the downscaled-to-1280px file if the camera original was
  larger.)

Worked example — a three-surface wash, mid-job:

- Driveway: 2 before / 2 after → documented. House siding: 1 before / 0
  after → not documented. Deck/Patio: 2 before / 2 after → documented.
  Checklist 5 of 6 (invoice not yet sent). No damage items.
- Summary reads **Surfaces documented 2 of 3 · Required photos 9 of 10 ·
  Checklist 83%**, status **INCOMPLETE** — twice over: the siding has no
  after photo, and the checklist is incomplete.
- Take the siding after photo and tick "Invoice sent" → status flips to
  **COMPLETE** (3 of 3 · 10 of 10 · 100%). Add a damage item without a
  note and it flips straight back to INCOMPLETE until the note is written.

## Data & privacy

Everything lives only in the visitor's own browser. Nothing is sent to a
server.

**localStorage:**

| Key | Contents |
|---|---|
| `surfaceproof.settings.v1` | Company name/phone/email + logo (data URL). |
| `surfaceproof.jobs.v1` | Saved job history (Pro): snapshots incl. verdict and photo ids. |
| `surfaceproof.draft.v1` | The in-progress job, so an interruption doesn't lose work. |
| `surfaceproof.pro.v1` | Pro unlock flag (see MVP note above). |
| `surfaceproof.freereport.v1` | Count of free PDF reports used (0 or 1 — see "First report free"). |

**IndexedDB:** database `surfaceproof`, object store `photos` — photo id →
photo data URL. Photos are deliberately **not** kept in localStorage (they
are far too big for its ~5 MB quota). If IndexedDB is unavailable in a
browser, the app falls back to keeping photos in memory only and tells the
user plainly in the "Your data" card that photos will be lost when the tab
closes. The "Your data" card also shows how many photos are stored and,
where the browser exposes it, approximate storage usage — photo libraries
grow fast, so users are told to export backups and clear old jobs.

The app tells users plainly: "Your data is stored only in this browser on
this device — export a backup regularly." On load it also calls
`navigator.storage.persist()` (best-effort, silent) to ask the browser to
keep the site's storage persistent and less likely to be evicted. Backup
export downloads a JSON file with settings, saved jobs, the current draft,
and all referenced photos (surface before/after **and** damage photos, as
data URLs); import restores all of it, photos included. Deleting a saved
job also deletes its photos (surface and damage) from IndexedDB.

Photo-handling guidance (also on the landing page's Privacy section, in
plain words): job photos belong to the cleaning business and the property
owner; get the owner's permission before photographing a property and before
sharing reports containing photos; photograph the surfaces and the work —
keep people, vehicle plates, and personal belongings out of frame where
possible; and export each job's evidence pack promptly into the business's
own storage, because the browser copy is the only copy until then.

## Deploy

Any static host works — copy the three files (or at least the two HTML files)
to the host as-is:

- **Netlify / Vercel:** drag-and-drop the folder, or connect the repo; no
  build command, publish directory = this folder.
- **Cloudflare Pages:** same — framework preset "None", no build command.
- **GitHub Pages:** fine for demos/preview only — GitHub Pages terms prohibit
  primarily commercial SaaS use, so don't run the paid product from there.

The only external requests the app makes are the jsPDF and JSZip CDN scripts
in `app.html` (cdnjs). If jsPDF fails to load, everything except the PDF and
evidence-pack buttons still works and the user gets a clear message; if only
JSZip fails, the PDF button still works and the evidence-pack button says
so plainly.
