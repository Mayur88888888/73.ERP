# MiniERP v1 — README

**Offline Single-File Production ERP for Plastic Molding / Press Shop**

**Author / Owner:** Mayur
**Version:** v12
**File:** `MiniERP.html` (single file, ~200 KB)
**Licence:** Internal use — Created by Mayur

---

Images
<img width="1827" height="825" alt="image" src="https://github.com/user-attachments/assets/eece58e3-2b66-4f84-8fb3-a63a50501411" />
----
----
<img width="1817" height="805" alt="image" src="https://github.com/user-attachments/assets/820859b5-d263-4375-9ef5-dad234eca161" />

----
----
<img width="1822" height="877" alt="image" src="https://github.com/user-attachments/assets/5188c985-ac0c-4c95-bfb6-1c1f6a10a643" />

----
----


<img width="1825" height="802" alt="image" src="https://github.com/user-attachments/assets/1f5b0377-3e06-475f-8707-e2f6a6ed370f" />

----
----
<img width="1817" height="835" alt="image" src="https://github.com/user-attachments/assets/ee076583-7030-40e4-a5fd-33132ac85972" />

----
----

## 1. Overview

MiniERP is a **fully offline, single-file HTML application** that runs in any modern browser by simply double-clicking the file. It provides production planning, material gating, machine scheduling, and a full document/audit trail for a plastic molding or press shop.

There is **no installation, no server, no internet, and no external dependencies**. All data lives in your browser's `localStorage`. Every CSV export carries the header `Created by Mayur`.

---

## 2. Quick Start

1. **Download** `MiniERP.html` to any folder on your computer.
2. **Double-click** the file — it opens in your default browser (Chrome, Edge, Firefox, Safari).
3. The app loads with **demo seed data** (3 machines, 6 items, 4 sales orders, 4 BOM rows) so you can explore immediately.
4. Start by clicking any tab at the top. Everything auto-saves.

> **Tip:** Bookmark the file path in your browser, or pin the tab. Do **not** open two tabs of MiniERP simultaneously — they will overwrite each other's state.

---

## 3. System Requirements

| Requirement | Detail |
|---|---|
| Browser | Chrome 90+, Edge 90+, Firefox 88+, Safari 14+ |
| Network | **None required** — works fully offline |
| Storage | ~5 MB localStorage per browser profile |
| Protocol | Works on `file://` (double-click) and `http://` |
| OS | Windows, macOS, Linux, Android, iOS |

---

## 4. Tab Reference (16 Tabs)

| # | Code | Tab | Purpose |
|---|------|-----|---------|
| 1 | OBK | Order Booking | Manage sales orders, material gating, molding hours |
| 2 | PLN | Planning / BOM | Single-level BOM with shortage roll-up |
| 3 | SCH | Machine Scheduling | Auto-scheduler with 3 shifts and Sunday control |
| 4 | UTL | Utilisation | SVG charts — bar, donut, stacked, line, heatmap |
| 5 | CTW | Control Tower | Read-only KPI snapshot and alerts |
| 6 | DLF | Deadline Feasibility | Per-order slack analysis |
| 7 | SHR | Shortage Roll-up | Aggregated child material shortages |
| 8 | SCN | Scenarios | Save / load / compare state snapshots |
| 9 | MLB | Multi-Level BOM | Recursive BOM explosion |
| 10 | MST | Machine & Item Master | Editable machines and items |
| 11 | LAB | Drag-Drop Lab | Visual drag-drop planning (Week / Month zoom) |
| 12 | ORG | Org Structure | Company, plants, storage, work centers |
| 13 | FLW | Document Flow | Linked document tree search |
| 14 | APR | Approvals Inbox | Pending approvals with audit trail |
| 15 | LDG | Material Ledger | Stock overview + movement history |
| 16 | LOG | Transaction Log | Chronological audit trail |

---

## 5. Key Concepts

### 5.1 Cycle Time
Cycle time is expressed in **minutes per part** (not hours). Molding hours are computed as:

```
molding_hours = max(0.5, round(qty × cycle_minutes / 60, 1))
```

### 5.2 Material Gating
A parent item cannot be molded unless **every** BOM child has enough stock.

| Availability Ratio | Status | Meaning |
|---|---|---|
| 100% | **OK** (green) | Fully moldable |
| 1–99% | **PARTIAL** (amber) | Only `floor(netQty × minRatio)` moldable |
| 0% | **BLOCK** (red) | Nothing moldable |
| Missing BOM | **HOLD** (red) | No recipe defined |

Availability is consumed **first-come-first-served** in schedule-date order (earliest SO wins).

### 5.3 Sundays — Working Toggle
By default **every Sunday is OFF** (grey hatched). To allow scheduling on a Sunday:

1. Go to **SCH** or **LAB** tab.
2. Find the **🔧 Working Sundays** panel (amber).
3. Tick the checkbox next to the ISO week you want to activate.

The auto-scheduler, drag-drop lab, and utilisation calculations all respect this toggle.

### 5.4 Loading Before Molding
Every job reserves **4 hours of loading** on the same machine before molding begins. Total slot = `loading + molding`.

### 5.5 Shifts
Three shifts per day, 8 hours each: **00–08, 08–16, 16–24**.

### 5.6 Document Numbering
`PREFIX-YYYY-NNNN` — plant-scoped, sequential.

| Prefix | Meaning |
|---|---|
| SO | Sales Order |
| MOP | Molding Order |
| REQ | Requirement |
| BUY | Buy Order |
| RCV | Receipt |
| CON | Consumption |
| FIN | Finished Receipt |
| JOB | Scheduled Job |
| BRK | Breakdown |
| MOVD | Movement Document |

### 5.7 Movement Codes (MiniERP-native, not SAP)

| Code | Direction | Meaning |
|---|---|---|
| 110 | + | Inward Receipt |
| 120 | + | Inward Return |
| 210 | − | Issue to Molding |
| 220 | − | Issue Scrap |
| 230 | − | Issue Rework |
| 310 | − | Plant Transfer Out |
| 320 | + | Plant Transfer In |
| 410 | + | FG Receipt |
| 420 | − | FG Rejection |
| 510 | + | Stock Adjustment + |
| 520 | − | Stock Adjustment − |
| 610 | + | Opening Balance |
| 910 | ± | Cancellation |

### 5.8 Status Codes

| Status | Badge | Colour |
|---|---|---|
| DRAFT | D | Grey |
| RELEASED | R | Blue |
| IN-PROGRESS | P | Amber |
| CONFIRMED | C | Teal |
| COMPLETED | X | Green |
| CLOSED | Z | Dark grey |
| CANCELLED | N | Red |

---

## 6. Daily Workflow

A typical planning cycle:

1. **OBK** — Enter sales orders from marketing. Review material status badges (red = blocked).
2. **PLN** — Verify BOM quantities. Flag any shortage.
3. **SHR** — Click **Generate Requirement** for each shortage. This creates `REQ-*` documents.
4. **APR** — Approve the requirements. Each approval auto-creates a `BUY-*` document.
5. **SCH** — Tick any working Sundays, then click **Run Auto-Scheduler**. Review the grid and holds.
6. **LAB** — (Optional) Drag parts to fine-tune placement. Click **Push to Main Schedule** to convert to locked jobs.
7. **UTL** — Review utilisation charts. Adjust horizon to 14 or 30 days for a wider view.
8. **CTW** — Final read-only snapshot before the day starts.

---

## 7. Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl + Z` / `Cmd + Z` | Undo |
| `Ctrl + Y` / `Cmd + Shift + Z` | Redo |
| `Enter` in command bar | Execute command |

---

## 8. Command Bar

The search box in the header accepts four types of input:

| Input | Example | Result |
|---|---|---|
| Nav code | `OBK` | Jump to Order Booking tab |
| Doc number | `SO-2026-0001` | Open Document Flow for that SO |
| Prefix | `MOP` | List latest 20 MOP documents |
| Shortcut | `create MOP` | Jump to OBK and trigger Create Molding Orders |

---

## 9. CSV Exports

Every CSV export begins with this header block:

```
Created by Mayur
Plant: PLT-01
Export: <Export Name>
Generated: 2025-01-15T10:30:00.000Z
Doc Type: MIXED

<header row>
<data rows>
```

**Available exports (15 total):**

1. Orders Export (OBK)
2. Planning Export — Orders + BOM + Holds (PLN)
3. Schedule Export — Jobs + Holds (SCH)
4. Utilisation Export — Per-machine + Item contribution (UTL)
5. Control Tower Snapshot (CTW)
6. Deadline Feasibility (DLF)
7. Shortage Roll-up (SHR)
8. Buy Order Draft (SHR)
9. Scenarios Summary (SCN)
10. Multi-Level BOM (MLB)
11. Org Structure (ORG)
12. Document Flow (FLW, per doc)
13. Approvals Log (APR)
14. Material Ledger (LDG)
15. Transaction Audit Log (LOG)

All CSVs are UTF-8 encoded and open cleanly in Excel, Numbers, and LibreOffice.

---

## 10. Data Storage & Backup

### Where data lives
All state is stored under the localStorage key `minierp_v12_fixed2` in your browser profile.

### Backup procedure
1. Open **LOG** tab.
2. Click **Export Audit CSV** — this gives you the full audit trail.
3. For a complete backup, use **SCN** → **Save Snapshot** with a name like `BACKUP-2025-01-15`.
4. To restore on another machine, you must manually copy the localStorage value (see advanced section below).

### Advanced: manual backup / restore
1. Open browser DevTools (`F12`).
2. Console → `copy(localStorage.getItem('minierp_v12_fixed2'))` to copy JSON.
3. Save the JSON to a text file.
4. To restore: `localStorage.setItem('minierp_v12_fixed2', '<paste JSON>')` then reload.

> **Warning:** Clearing browser data, using private/incognito mode, or switching browsers will lose your data. Back up weekly.

---

## 11. Customisation

### Adding a new item
1. Go to **MST** tab.
2. Click **+ Add Item**.
3. Fill in the material number, description, type (FG/SFG/RM), unit, cycle time (minutes per part), lead time (days), and storage location.

### Adding a new machine
1. Go to **MST** tab → **+ Add Machine**.
2. Or in **ORG** tab, edit the Work Centers list.

### Adding a new BOM row
1. Go to **PLN** tab → **+ Add BOM Row**.
2. Select parent (FG) and child (RM/SFG), enter per-unit consumption.

### Changing company info
Go to **ORG** tab → edit the Company name field directly.

---

## 12. Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| Data disappeared | Cleared browser data | Restore from backup JSON |
| Two tabs disagree | Opened MiniERP twice | Close one tab, reload the other |
| Scheduler places nothing | Sundays all OFF, or no capacity | Tick a working Sunday or extend horizon to 14/30 days |
| Material BLOCK everywhere | BOM available qty too low | Edit BOM available qty in **PLN** tab |
| Drag-drop not working | Browser blocks native DnD on `file://` in some cases | Use Chrome or Edge; or use **SCH** auto-scheduler instead |
| CSV won't open in Excel | Rare encoding issue | Open with "Data → From Text" and choose UTF-8 |
| Undo stops working | 40-step limit reached | Expected; older steps are dropped |

---

## 13. Technical Notes

### What MiniERP is NOT
- Not a multi-user system (single browser profile)
- Not connected to any ERP, database, or network
- Not a replacement for a real MRP/ERP for compliance purposes
- Not certified for financial reporting

### What MiniERP IS
- A **planning aid** for a single planner or supervisor
- A **what-if simulator** for material and capacity scenarios
- A **portable offline tool** that runs from a USB stick
- A **CSV data source** for downstream Excel analysis

### Browser APIs used
- `localStorage` — persistence
- `Blob` + `URL.createObjectURL` — CSV downloads
- Native HTML5 drag-and-drop
- Inline SVG for all charts
- `crypto.randomUUID` fallback is not required (IDs are sequence-based)

### No external dependencies
- Zero CDN, zero `<script src>`, zero `<link href>`
- No Google Fonts (system font stack)
- No Chart.js, D3, or any charting library — all charts are hand-drawn SVG

---

## 14. Version History

| Version | Notes |
|---|---|
| v11.1 | Original core: OBK, PLN, SCH, UTL, CTW, DLF, SHR, SCN, MLB, MST, LAB |
| v12 | Added ORG, FLW, APR, LDG, LOG tabs; document service; real dates in Lab; per-week Sunday toggles; editable tables; advanced SVG charts |

---

## 15. Support & Ownership

**Author:** Mayur
**Owner:** Mayur
Every header, footer, and CSV export in this tool displays **"Created by Mayur"**.

For issues, feature requests, or custom extensions, contact Mayur directly.

---

## 16. Safety & Best Practices

1. **Back up weekly** using the Scenario snapshot feature.
2. **Do not** open MiniERP in two tabs at once.
3. **Do not** use private/incognito mode for real data.
4. **Do not** clear browser site data without a backup.
5. **Validate** all CSVs before feeding them into downstream systems.
6. **Review** the Control Tower alerts every morning before running the scheduler.

---

*MiniERP — Created by Mayur. Offline. Single-file. Yours.*
