# IPTV-channel-reduction-strategy

# The Idiot's Guide to Curating a Lean 400–480 Channel IPTV Lineup in Dispatcharr

> **Goal:** Take a bloated, multi-thousand-channel IPTV playlist and strip it down to a laser-focused **400 to 480 channel lineup** that follows strict rules:
> - **Zero Sports** (no PPV, football, NFL, UFC, F1, etc.)
> - **English Only** (strict regional priority: **UK → USA → NZ → AU**)
> - **Zero Duplicates** (4K/UHD preferred, automatically falling back to FHD/HD/SD)
> - **Zero Provider Group Clutter** (no `## UK ENTERTAINMENT ##` or `UK |` prefixes)
> - **100% EPG Coverage** (no blank "No Program Information Available" channels)

---

## Before You Start: Back Up Your Setup
Before running bulk edits or plugins, create a safety net:
1. Open **Dispatcharr** in your web browser (`http://<your-server-ip>:9191`).
2. Navigate to **Settings** (left sidebar or top menu) → **Backup & Restore** (or back up your `/data` Docker volume).
3. Click **Create Backup** and download a copy to your computer.

---

## Phase 1: Drop Non-English & Sports Groups at Import Time
Dispatcharr separates raw **Streams** (from your provider) from your curated **Channels** (what your TV sees). Never import everything at once.

### Step 1.1: Configure Your Provider & Group Filter
1. In the left navigation bar, click **M3U & EPG Manager** (or **Providers**).
2. Locate your IPTV provider under **M3U Sources** and click the **Edit** (pencil) or **Groups / Filter** icon.
3. Review the list of provider groups and toggle **OFF** (disable) every group that does not match your rules:
   - **Turn OFF all non-English countries** (keep only **UK**, **USA**, **NZ**, and **AU** groups).
   - **Turn OFF all Sports & Event groups** (anything labeled `Sports`, `PPV`, `EFL`, `NFL`, `NBA`, `UFC`, `F1`, `Sky Sports`, `TNT Sports`, `bein`, etc.).
   - **Turn OFF VOD groups** (`Movies`, `Series`, `24/7`—unless you explicitly want 24/7 channels).
4. **CRITICAL SETTING:** Ensure **Auto Channel Sync** (or *Auto-Create Channels*) is turned **OFF**.
   - *Why?* If you leave Auto Sync on, Dispatcharr will import your provider's messy group names (`## UK GENERAL ##`) and create thousands of duplicate channels.
5. Click **Save** and trigger a **Refresh / Update** on the M3U source so Dispatcharr only indexes your allowed English non-sports streams.

---

## Phase 2: Load Clean EPG (TV Guide) Sources
You want EPG sources loaded *before* matching channels, ordered strictly by your regional priority (**UK → USA → NZ → AU**).

### Step 2.1: Add & Prioritize EPG Feeds
1. Go to **M3U & EPG Manager** → **EPG Sources** tab.
2. Click **Add EPG** and enter your XMLTV URLs (your provider's EPG URL plus any reliable free XMLTV feeds like EPGShare01 for UK, US, NZ, and AU).
3. Name your EPG sources clearly with country prefixes so automation plugins can filter them easily:
   - `UK - Main Guide`
   - `USA - Main Guide`
   - `NZ - Main Guide`
   - `AU - Main Guide`
4. Drag or set the **Priority Order** of your EPG sources from top to bottom:
   1. **UK** (Highest Priority)
   2. **USA**
   3. **NZ**
   4. **AU**
5. Click **Save** and click **Refresh All EPGs**. Wait for the status to show green/idle before continuing.

---

## Phase 3: Install Your Automation Plugins
Instead of manually merging 4K, HD, and SD duplicates or hunting down broken TV guides, install three community plugins from the Dispatcharr Plugin Hub.

### Step 3.1: Install Lineuparr, EPG Suggester, and EPG Janitor
1. In the left navigation bar, click **Plugins**.
2. Click the **Find Plugins** (or **Plugin Hub**) tab.
3. Search for and click **Install** on each of the following plugins:
   - **Lineuparr** (`Dispatcharr-Lineuparr-Plugin`) — *Matches streams against clean channel lineups, deduplicates channels, and automatically orders streams by quality: 4K → UHD → FHD → HD → SD.*
   - **EPG Suggester** (`epg_suggester`) — *Scores and bulk-assigns the best XMLTV guide match to every channel.*
   - **EPG Janitor** (`Dispatcharr-EPG-Janitor-Plugin`) — *Finds and cleans up any channel with missing or broken guide data.*
4. Reload the **Plugins** page in your browser so all three appear under **Installed Plugins**.

---

## Phase 4: Deduplicate, Prioritize 4K, and Cap at 400–480 Channels
We will use **Lineuparr** (combined with clean Dispatcharr Channel Groups) to build your target 400–480 channel roster in **UK → USA → NZ → AU** order.

### Step 4.1: Create Clean Channel Groups (No `##` Junk)
1. Go to **Channels** in the left menu → click **Manage Groups** (or **Groups**).
2. Create a simple, clean folder structure (5 to 7 folders maximum):
   - `1. UK TV`
   - `2. USA TV`
   - `3. NZ TV`
   - `4. AU TV`
   - `5. Movies & Docs` *(optional if you prefer genre grouping over country grouping)*

### Step 4.2: Run Lineuparr in Regional Priority Order (UK → USA → NZ → AU)
1. Go to **Plugins** → click **Lineuparr** → open **Settings**.
2. Set **M3U Source** to your active provider (or `All sources`).
3. Set **Quality Priority** to default (`4K > UHD > FHD > HD > SD` — Lineuparr automatically places 4K/UHD streams at the top of each channel's stream list and keeps FHD/HD/SD streams underneath as automatic failover backups).
4. Run Lineuparr in four passes—one per region—to control your exact channel budget:
   - **Pass 1 (UK — Target ~180–200 channels):**
     - Select a UK Lineup File (e.g., `UK_Freeview` / `UK_Sky` non-sports categories).
     - Set **EPG Sources for Matching** to `UK*`.
     - Click **Preview Stream Match** (dry run) and check the CSV in `/data/exports/` to verify matches.
     - Click **Full Sync**.
   - **Pass 2 (USA — Target ~140–160 channels):**
     - Turn **ON** *Preserve Existing Streams* (so it appends rather than wiping your UK channels).
     - Select a USA Lineup File (exclude sports categories).
     - Set **EPG Sources for Matching** to `USA*`.
     - Click **Preview Stream Match**, verify, then click **Full Sync**.
   - **Pass 3 (NZ — Target ~40–60 channels):**
     - Keep *Preserve Existing Streams* **ON**.
     - Select the NZ Lineup File and set **EPG Sources for Matching** to `NZ*`.
     - Click **Preview Stream Match** → **Full Sync**.
   - **Pass 4 (AU — Target ~40–60 channels):**
     - Keep *Preserve Existing Streams* **ON**.
     - Select the AU Lineup File and set **EPG Sources for Matching** to `AU*`.
     - Click **Preview Stream Match** → **Full Sync**.

*(Note: If you prefer building your own exact 450-channel list, copy one of Lineuparr's `*_lineup.json` files in `/data/plugins/lineuparr/`, trim it down to your exact 450 non-sports channels in UK → USA → NZ → AU order, and run a single Full Sync.)*

---

## Phase 5: Strip Residual Prefixes (`UK |`, `##`, `4K`) via Regex
If any channel names still contain provider tags or resolution labels, clean them in bulk in 30 seconds:

### Step 5.1: Bulk Regex Rename
1. Go to **Channels** in the left sidebar.
2. Check the **Select All** box at the top of the channel table.
3. Click **Bulk Edit** (or **Edit Selected**) at the top action bar.
4. Locate the **Regex Rename** tool and run these two passes:
   - **Pass A — Remove leading country prefixes & `##` symbols:**
     - **Find:** `^(?:#+\s*|(?:UK|US|USA|NZ|AU)\s*[\|:\-]\s*)`
     - **Replace:** *(leave completely empty)*
     - Click **Apply / Save**.
   - **Pass B — Remove trailing quality tags (`4K`, `UHD`, `FHD`, `HD`, `SD`, `RAW`):**
     - **Find:** `\s*(?:\[|\()?(?:4K|UHD|FHD|1080P|HD|720P|SD|RAW)(?:\]|\))?$`
     - **Replace:** *(leave completely empty)*
     - Click **Apply / Save**.

*Result:* A channel named `UK | BBC One 4K` becomes `BBC One`, while Dispatcharr silently keeps the `4K` stream as primary and `FHD/HD` as backups underneath.

---

## Phase 6: Enforce 100% EPG Coverage & Lock the 400–480 Cap
Now we eliminate every single channel that lacks working TV guide data and lock your final count between **400 and 480 channels**.

### Step 6.1: Fill Any Missing EPGs with EPG Suggester
1. Go to **Plugins** → **EPG Suggester**.
2. Click **Scan / Audit Unmatched Channels**.
3. Review the suggested matches (sorted by confidence score) and click **Bulk Apply High-Confidence Matches**.

### Step 6.2: Purge Dead/Blank EPG Channels with EPG Janitor
1. Go to **Plugins** → **EPG Janitor**.
2. Run **Scan for Missing / Broken EPG** (Preview mode first).
3. Review the exported report of channels showing `"No Program Information Available"` or zero guide data.
4. Run **Clean / Unassign Broken EPGs** (or delete those EPG-less channels directly in the **Channels** tab by filtering for **EPG = None / Unassigned** → **Select All** → **Delete**).

### Step 6.3: Trim to Your Strict 400–480 Ceiling
1. Go to **Channels** and look at the **Total Channels** counter at the top/bottom of the list.
2. If your total is above **480**:
   - Open your lowest-priority region group (**AU** first, then **NZ**, then niche **USA** cable channels).
   - Select surplus channels until your total sits comfortably between **400 and 480** (aim for **~440–460** to leave headroom).
   - Click **Delete** on the surplus channels.
3. Verify your channel numbering order matches your priority:
   - **Ch 1–200:** UK
   - **Ch 201–360:** USA
   - **Ch 361–420:** NZ
   - **Ch 421–480:** AU

---

## Phase 7: Lock Everything into a Dedicated Channel Profile
Never point your TV player at Dispatcharr's "All Channels" endpoint—always use a **Channel Profile** so accidental imports never leak onto your TV.

### Step 7.1: Create & Export Your Profile
1. Go to **Channels** → **Channel Profiles** (or **Profiles** in the menu).
2. Click **Add Profile** and name it `Master 450`.
3. Assign your curated **400–480** channels to `Master 450` and click **Save**.
4. Copy the **M3U URL**, **XMLTV EPG URL**, or **HDHomeRun Tuner URL** specifically associated with the `Master 450` profile.
5. Paste those URLs into your TV client (**TiviMate**, **Plex**, **Jellyfin**, or **Emby**).

---

## Quick Maintenance Checklist (Once a Month)
- [ ] **M3U & EPG Manager:** Verify your provider M3U and XMLTV feeds are refreshing on schedule.
- [ ] **Plugins → Lineuparr:** Run **Full Sync** (with *Preserve Existing Streams* ON) if your provider adds new 4K feeds or updates stream URLs.
- [ ] **Plugins → EPG Janitor:** Run a quick scan to ensure zero EPG-less channels crept into your lineup.
- [ ] **Settings → Backup:** Download a fresh database backup after any major tweak.
