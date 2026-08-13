# Fight for Illinois Schools — hosting the district data

There are two ways to host the data. Option A (repo + GitHub Pages) is what I'd
recommend. Option B (Gist) is what you asked about and works fine — the notes
and the gotchas are both below.

---

## What the files are

| File | Size | What it is |
|---|---|---|
| `index.html` | 61 KB | The page itself |
| `support.js` | 69 KB | Runtime the page needs — must sit beside index.html |
| `districts-index.json` | 51 KB | Just names, cities and district type. Loaded on page load to fill the dropdown |
| `districts.json` | 546 KB | Every number for all 864 districts. Fetched **only** when a visitor first picks a district |
| `report-card.html` | 15 KB | The printable one-page report card, built on demand for any district |
| `hero.png` | 147 KB | The header photograph |
| `ffi-logo.jpg` | 65 KB | The logo in the report-card footer |

The page now loads in about 180 KB. The big 546 KB data file is not touched until
somebody actually selects a district, and it is fetched once per visit and reused
after that.

**Important:** the `.xlsx` itself cannot go online as-is — a web page can't read an
Excel file. `districts.json` **is** your spreadsheet, converted. That is the file to
host. Send me a new spreadsheet any time and I'll regenerate both JSON files.

---

## Option A — repo + GitHub Pages (recommended)

1. Go to your repo on github.com → **Add file → Upload files**.
2. Drag in all seven files at once.
3. Commit message: `Add all 864 districts` → **Commit changes**.
4. If Pages isn't on yet: **Settings → Pages** → Source **Deploy from a branch**,
   Branch **main**, folder **/ (root)** → Save.
5. A minute later it's live at `https://YOUR-USERNAME.github.io/YOUR-REPO/`.

Embed it on your site with a Custom HTML block:

    <iframe src="https://YOUR-USERNAME.github.io/YOUR-REPO/"
            style="width:100%;border:0;height:100vh" title="Fight for Illinois Schools"></iframe>

Nothing else to configure — the page finds its data by relative path.

---

## Option B — data in a GitHub Gist

Do this if you want the data to live somewhere separate from the page.

### 1. Create the Gist

1. Go to **gist.github.com**.
2. In the filename box type `districts.json`.
3. Open `districts.json` from this folder in a text editor, select all, copy, paste
   into the Gist body. (It's one long line — that's fine.)
4. Click **Add file** and repeat for `districts-index.json`.
5. Click **Create public gist**. A secret gist works too, but the raw URL still has
   to be publicly reachable, so public is simpler.

### 2. Get the two raw URLs

On the Gist page, click **Raw** above each file and copy the address bar. Each looks like:

    https://gist.githubusercontent.com/YOUR-USERNAME/GIST-ID/raw/districts.json

Strip any long hex commit hash out of the middle if one is there — without it, the
URL always serves the newest version, so future updates go live automatically.

### 3. Point the page at them

Paste this **immediately above** the page markup (or in your site's header, before
the page loads):

    <script>
      window.FFI_INDEX_URL = 'https://gist.githubusercontent.com/YOUR-USERNAME/GIST-ID/raw/districts-index.json';
      window.FFI_DATA_URL  = 'https://gist.githubusercontent.com/YOUR-USERNAME/GIST-ID/raw/districts.json';
      window.FFI_CARD_URL  = 'https://YOUR-USERNAME.github.io/YOUR-REPO/report-card.html';
    </script>

`report-card.html` needs the same treatment — open it and change the two lines at
the top of its `<script>` block:

    var DATA_URL = 'https://gist.githubusercontent.com/YOUR-USERNAME/GIST-ID/raw/districts.json';
    var LOGO_URL = 'https://YOUR-USERNAME.github.io/YOUR-REPO/ffi-logo.jpg';

### 4. To update the data later

Open the Gist → **Edit** → replace the file contents → **Update public gist**. The
site picks it up with no redeployment.

### Gist gotchas worth knowing

- Raw Gist URLs are served through a CDN that caches for roughly five minutes, so
  an edit can take that long to appear.
- Raw Gist URLs are rate-limited and not meant for heavy traffic. If the page gets
  busy, move the two JSON files into the repo (Option A) — GitHub Pages is built
  for serving files and the page needs no changes beyond deleting the override script.
- Everything else (`index.html`, `support.js`, `report-card.html`, `hero.png`,
  `ffi-logo.jpg`) still has to live somewhere real. A Gist cannot host the page.

---

## How the report-card PDFs work

There is no file per district. **Download Full Report Card (PDF)** opens
`report-card.html?d=<district-slug>`, which builds that district's card from the
data and opens the browser's print dialog, where the reader chooses "Save as PDF".
864 report cards from one 15 KB file.

Direct links work: `.../report-card.html?d=bellwood-sd-88`.
Add `&print=0` to view a card without the print dialog opening.

---

## Notes on the data

- 864 districts: 483 High School / Unit, 381 Elementary.
- Suppressed or missing values read `n/a` everywhere.
- Percentages rounded to whole numbers.
- Enrollment-by-race in the header uses the spreadsheet's own whole-number string (column L).
- Elementary districts show the reading-by-grade table; High School / Unit districts show
  the graduation rate instead, matching your Manteno sample.
- The spending banner is taken verbatim from column AM.
