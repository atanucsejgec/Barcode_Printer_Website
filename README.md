# TSC TE244 — 2-Up Barcode Label Printer (38×38mm stamps)

First-time setup for printing barcode labels on a TSC TE244 with a **79mm × 38mm** die-cut roll (two 38×38mm stamps per row + 3mm horizontal gap + 3mm vertical gap between rows).

If prints come out shifted, cut in half, faint, or with a blank stamp between good ones, you skipped one of the steps below.

---

## 1. Install the TSC TE244 driver

1. Download the driver from tscprinters.com → Support → Downloads → **TE244**.
2. Install it and plug the printer in via USB.
3. Verify it appears under **Settings → Bluetooth & devices → Printers & scanners** as `TSC TE244`.

---

## 2. Configure Printing Preferences (one time)

Open **Settings → Printers & scanners → TSC TE244 → Printing preferences**.
There are 5 tabs — set each one as follows.

### 📄 Page Setup tab
| Field | Value |
|---|---|
| Stock → Name | **Barcode79x41 (79.0 mm × 38.0 mm)** |
| Orientation | **Portrait** |

If the stock doesn't exist yet, click **New…** and use the values from the "Edit Stock" section below.

#### Edit Stock (click New or Edit under Stock)
| Field | Value |
|---|---|
| Name | `Barcode79x41` |
| Type | **Die-Cut Labels** |
| Width | **79.0 mm** |
| Height | **38.0 mm** &nbsp;⚠️ *label only, NOT 41* |
| Exposed Liner — Left | **1.3 mm** |
| Exposed Liner — Right | **1.3 mm** |

### 🎨 Graphics tab
| Field | Value |
|---|---|
| Resolution | **8.0 dpmm × 8.0 dpmm** (= 203 DPI, only option on TE244) |
| Dithering | **None** ⚠️ *not Halftone or Error Diffusion — hard edges scan best* |
| Color Control | **Monochrome** |

### 📦 Stock tab (Media Settings)
| Field | Value |
|---|---|
| Method | **Direct Thermal** ⚠️ *set to Thermal Transfer ONLY if you have a ribbon installed* |
| Type | **Labels With Gaps** |
| Gap Height | **3.0 mm** |
| Gap Offset | **0.0 mm** |
| Post-Print Action | **None** (or **Tear-Off** if you want auto-advance to tear line) |
| Feed Offset | **0.0 mm** (adjust ±0.5mm only if consistently printing high/low) |
| Position Adjustments → Use Current Printer Settings | ✔ checked |

### ⚙️ Options tab
| Field | Value |
|---|---|
| Print Speed | **50.80 mm/sec** (= 2 ips — slowest, sharpest) |
| Darkness | **10–12** ⚠️ *NOT 15 — max darkness causes bar bleed & scan failures* |
| Direct to Buffer | Automatic |
| Stored Graphics | Automatic |
| Disable printer immediate commands | ☐ unchecked |
| Enable grayscale image printing | ☐ **unchecked** ⚠️ *turns barcodes into fuzzy grey, do not enable* |

Click **Apply** → **OK**.

---

## 3. Calibrate the roll (after loading a new roll)

The printer needs to learn where the gaps between labels are.

1. Load the roll and close the printer lid.
2. Power the printer **OFF**.
3. Hold down the **FEED** button.
4. While holding FEED, power the printer **ON**.
5. Release FEED after it feeds out **2–3 labels** and stops.

Status LED should be solid green. If it blinks red, re-open the printer, re-seat the roll straight, and repeat.

Before every print job, tap **FEED** once to park the roll at the start of a fresh label.

---

## 4. Print from the browser

1. Open `index.html` in **Google Chrome** (Edge also works).
2. Fill in the form (see section 6 below for what each field means).
   - **Copies = number of rows.** Each row prints 2 stamps. So `Copies = 3` → 6 stamps.
3. Click **🖨️ Print Labels**.

### Chrome print dialog — critical settings
| Setting | Value |
|---|---|
| Destination | **TSC TE244** |
| Paper size | **Barcode79x41** (the stock you created in step 2) |
| Copies | **1** ⚠️ *do NOT change this — copies are built into the document* |
| Margins | **None** |
| Scale | **Default (100)** — NOT "Fit to page" |
| Background graphics | **ON** (under "More settings") |
| Headers and footers | **OFF** |

Click **Print**.

---

## 5. Increasing Print Quality

Try these in order, cheapest first.

### A. Clean the printhead (biggest gain)
Power off → open lid → wipe the thin black bar across the top with a **cotton swab dipped in 99% isopropyl alcohol**. Let it dry 30 seconds before closing. Do this every 1–2 rolls, or immediately if you see vertical white streaks.

### B. Tune Darkness on the Options tab
- Start at **10**. Print one label.
- If bars look grey / text looks thin → raise by 1 and re-print.
- If bars start merging into each other or text edges look burnt / fat → lower by 1.
- Sweet spot on most rolls: **10–12**. Never leave it at 15 unless the paper is unusually thick.

### C. Keep Speed at 50.80 mm/sec (2 ips)
Faster speeds = less time for the printhead to fully darken each dot. Only raise speed if you're printing hundreds of labels and quality is already perfect.

### D. Match Method to your media
- If labels turn black when you scratch them hard with a fingernail → **Direct Thermal** media → set Method to Direct Thermal.
- If the labels came with a wax/resin ribbon cartridge → **Thermal Transfer** → install the ribbon and set Method to Thermal Transfer.
- Wrong Method = blank or extremely faint output.

### E. Use good thermal paper
Cheap rolls fade to grey, print unevenly, and darken over time on the shelf. Look for **top-coated direct thermal** stock. Store rolls cool, dark, and sealed — heat & sunlight pre-darken the paper before you even print.

### F. Increase barcode bar width in the HTML
In `index.html`, find the `barcodeOptions` object inside `generateLabels()`:
```js
const barcodeOptions = {
    format,
    width: (format === "EAN13" || format === "UPC") ? 1.15 : 1.35,  // ← module width in px
    height: 32,        // ← try 40 for taller bars
    displayValue: false,
    margin: 0
};
```
Raising `width` makes each bar thicker and easier to scan, but at some point the barcode overflows the 32mm SVG box. If you push it up, also reduce `.barcode-svg { width: 32mm; }` in the CSS to `28mm` so it still fits.

### G. Keep text bold and at least 6pt
At 203 DPI the printhead can't resolve thin fonts under 6pt. Bold weights (700+) always print cleaner than regular weights at small sizes.

---

## Troubleshooting

### Half-print / labels come out shifted
- Forgot calibration → redo step 3.
- Stock Height is set to 41 instead of 38 → fix in Edit Stock.
- Chrome Scale is "Fit to page" → change to Default (100).
- Chrome Copies > 1 → set to 1, use the app's Copies field instead.

### A blank label prints between good ones
- Chrome Copies > 1 while the app already has copies. Set Chrome Copies to 1.
- Post-Print Action is set to something other than None/Tear-Off.

### Print is blank or extremely faint
- **Method mismatch** — Direct Thermal media with Thermal Transfer mode selected (or vice versa). See section 5D.
- Printhead needs cleaning (section 5A).
- Ribbon exhausted (if using Thermal Transfer).

### Vertical white streaks / lines through the barcode
- Printhead has debris on it → clean it (section 5A).
- If streaks persist after cleaning, the printhead has a dead pixel — needs replacement.

### Bars are grey, not black
- Raise **Darkness** (10 → 12).
- Cheap or old thermal paper. Swap the roll.

### Bars are merged / text looks fat and burnt
- Lower **Darkness** (15 → 11).
- Lower Speed if darkness alone can't fix it.

### Barcode is at the very edge of the right stamp
The 3mm horizontal gap between stamps on each row is physical (roll can't change it). In `index.html` you can push barcodes further from that edge:
- `.label-left  { padding-right: 3mm; }` → increase to `4mm`
- `.label-right { padding-left:  3mm; }` → increase to `4mm`
- `.barcode-svg { width: 32mm; }` → decrease to `30mm`

### Printer keeps beeping / red LED
- Roll loaded crooked or empty.
- Re-calibrate (step 3).

---

## 6. Market-standard label fields

The generator produces a retail-grade label with all fields required for packaged consumer goods sold in India (and compatible with most global retail formats). Every field is optional in the sense that empty ones just print blank — but for a compliant label you should fill all of them.

### Label fields

| Field | What it is | Example |
|---|---|---|
| **Store Name** | Your brand or shop name. Prints bold at the top. | `MY STORE` |
| **Product Name** | Full product description. Up to 2 lines. | `Premium Basmati Rice` |
| **Net Weight / Qty** | Net contents in g / kg / ml / L / pcs. Legally required for packaged goods. | `100 g`, `1 kg`, `500 ml`, `12 pcs` |
| **MRP** | Maximum Retail Price, inclusive of all taxes. The `₹` symbol is safe on the TE244. | `₹199.00` |
| **Batch / Lot No.** | Traceability code so a batch can be recalled if needed. Keep it short (6–8 chars). | `B2607A` |
| **Packed / Mfg Date** | Month/year of packing or manufacture. | `07/2026` |
| **Best Before / Use By** | Shelf life expressed as an absolute date OR a duration from packing. | `07/2027` or `6 months from packing` |
| **Barcode Format** | See table below. Pick the standard your point-of-sale expects. | `CODE128` |
| **Barcode Data** | The number or text encoded in the bars. | `1234567890` |

### Barcode format guide

| Format | Use it when | Data rules |
|---|---|---|
| **EAN-13** | You sell products with a globally-registered GTIN and expect them to be scanned at any retail POS worldwide. **The retail standard outside North America.** | Exactly **12 digits** (app auto-computes the 13th checksum) or **13 digits** (uses as-is). Non-digits are stripped. |
| **UPC-A** | Same as EAN-13 but you're selling into US / Canadian retail. | **11 digits** (checksum auto-added) or **12 digits**. |
| **CODE128** | In-store SKU, internal inventory tag, or any alphanumeric code. Very compact, very reliable, no registration needed. **Default choice for shop-generated tags.** | Any ASCII text. |
| **CODE39** | You need to interoperate with older warehouse / industrial scanners that don't understand CODE128. | Uppercase letters, digits, and `- . $ / + % SPACE`. Lowercase is auto-uppercased. |

If you pick EAN-13 or UPC-A and the data isn't the right length, the app **automatically falls back to CODE128** and shows a warning below the format picker so nothing prints wrong.

### GTIN / EAN registration
Real EAN-13 numbers must be issued by GS1 (gs1india.org in India, gs1us.org in the US, etc.) — you can't invent them. If you make up an EAN-13, it will still scan, but it may collide with someone else's product at a POS. For in-store use with your own scanner, **use CODE128** and don't worry about registration.

---

## Files in this project
- `index.html` — the label generator + printer page. Open in Chrome to use.
- `README.md` — this file.

## Hardware summary
- Printer: **TSC TE244** (203 DPI / 8 dpmm, direct thermal capable, also supports thermal transfer with ribbon)
- Media: 79mm-wide die-cut roll, 38×38mm labels, 3mm horizontal gap, 3mm vertical gap
