# Site Map: noguchi.ug.edu.gh

Reference doc for troubleshooting. Keep this updated when plugins, themes,
or content structure change significantly (e.g. after a migration, a new
section is built, or a major plugin swap).

**Last updated:** 2026-06-26

## Platform

- **CMS:** WordPress
- **Active theme:** Hello Elementor (minimal canvas theme, all real design
  work happens in Elementor — do not expect the theme itself to control
  layout/styling)
- **Page builder:** Elementor + Elementor Pro
- **Dynamic content engine:** JetEngine (Crocoblock) — powers custom post
  types, custom fields, and "Listing" templates that repeat content (staff
  grids, project cards, publication lists, etc.)
- **Security:** Wordfence Security (active)
- **Backup:** All-in-One WP Migration and Backup (active)

## ⚠️ Security Note (flagged 2026-06-26)

A plugin called **"Acazycho"** (slug `acazycho`) is installed with a
nonsensical description and an unrelated "vendor site" link
(`the556.com`) that has no connection to the plugin's name. This is a
strong red flag for a malicious/spam plugin, possibly planted via a
compromised account, vulnerable plugin, or supply-chain issue.

- Status at time of writing: **inactive** (not currently running).
- **Recommended action:** Do not activate it. Confirm with your
  IT/security contact, run a full Wordfence scan, and if nothing
  legitimate depends on it, delete it entirely from
  **Plugins → Acazycho → Delete**. Also worth: rotate admin passwords and
  review the **Users** list for any unfamiliar admin accounts, since
  rogue plugins are often installed via a compromised login rather than
  a vulnerability in this specific case.

## Installed Plugins (31 total)

Active unless noted otherwise.

| Plugin | Role |
|---|---|
| ~~Acazycho~~ | **Suspicious — see security note above. Inactive.** |
| All-in-One WP Migration and Backup | Site backup/migration |
| BetterDocs | Knowledge base / FAQ pages |
| Classic Editor | Enables old-style post editor alongside Elementor |
| Disable Comments | Globally disables comments |
| Elementor / Elementor Pro | Page builder (core dependency for all page layouts) |
| ElementsKit Lite / ElementsKit Pro | Extra Elementor widgets |
| Essential Addons for Elementor | Extra Elementor widgets |
| EventON | Events calendar |
| Imagify | Image compression/optimization |
| JetBlocks For Elementor | JetEngine/Elementor integration widgets |
| JetBlog For Elementor | Blog-related widgets |
| JetElements For Elementor | JetEngine/Elementor widgets |
| **JetEngine** | **Core: custom post types, custom fields, Listing Grids/Templates** |
| JetEngine - Custom visibility conditions | JetEngine add-on |
| JetEngine - Trim string callback | JetEngine add-on |
| JetFormBuilder (+ Limit Form Responses, Save Form Progress) | Forms |
| JetSearch | Site search |
| JetSmartFilters | Filtering for listings |
| JetStyleManager | JetEngine styling |
| JetTabs For Elementor | Tabs widget |
| JetThemeCore | JetEngine theme integration |
| JetTricks | JetEngine animation/utility add-on |
| Rank Math SEO | **Inactive** — SEO plugin, currently disabled |
| Site Kit by Google | Analytics/Search Console integration |
| Wordfence Security | Security/firewall/malware scanning |
| WP 2FA | **Inactive** — two-factor auth, currently disabled |

## Custom Post Types (JetEngine → Post Types)

| Name | Slug |
|---|---|
| Awards | `awards` |
| Admin Staff | `admin-staff` |
| Key Research Activities | `research-activities` |
| Research Staff | `_researchstaff` |
| Members | `fellows` |

Note: the "Members" post type (slug `fellows`) is the one used for
Research Scientists/Fellows, Project Leads, and Research Staff cards —
the *role* shown on a card comes from fields on the Member post (Member
type: Fellow/Staff, Designation, Roles), not from separate post types per
role.

## Listing Templates (JetEngine → Listings/Components)

These are the reusable "card" templates that JetEngine repeats per post.
**If a whole grid of cards breaks identically (same field missing on every
card), the bug is almost always in one of these templates, not in
individual posts.**

| Template | Post ID | Used for (post type) |
|---|---|---|
| Fellow Listing Template | 138 | Members — "Research Scientists" cards |
| Research Staff Listing Template | 161 | Members — "Research Staff" cards |
| Project Lead Listing Template | 134 | Members — Project Lead cards |
| Admin Staff Listing Template | 7341 | Admin Staff |
| Research Project Listing Template | 661 | Key Research Activities |
| Awards Listing | 13581 | Awards |
| Tubercuosis | 12067 | Publications |
| Kwame | 20973 | Posts (general) |
| Funder Listing Template | 130 | Repeater field (used inside another listing, not a top-level post type) |
| External Collaborators Listing Template | 127 | Repeater field |
| Internal Collaborators Listing Template | 122 | Repeater field |
| CE Listing Template | 112 | Repeater field |
| Current Project (Fellow) | 68 | Repeater field |
| Key Publications | 28 | Repeater field |
| Focus of Work (Fellow) | 23 | Repeater field |
| Roles of Fellow | 16 | Repeater field |

To edit any of these: **wp-admin → JetEngine → Listings/Components →
[name] → Edit with Elementor**.

## Known Past Issues

- **2026-06-26:** Staff photos disappeared site-wide after an edit to the
  Fellow Listing Template — a Dynamic Tag binding got cleared. Full
  writeup: [staff-photos-fix.md](staff-photos-fix.md).

## General Troubleshooting Playbook

### "The whole site is broken" after a plugin update

This is almost always one plugin conflicting with another (often Elementor
add-ons conflicting with each other, or a JetEngine add-on version
mismatch). Standard process:

1. **Don't panic-delete anything.** Deactivating is reversible and safe;
   deleting a plugin can lose its settings.
2. Go to **wp-admin → Plugins**. If the *admin area itself* is broken/blank,
   you may need to disable plugins via the database or hosting file manager
   (rename `wp-content/plugins/<folder>` via FTP/file manager) — ask before
   doing this if you're not comfortable with FTP.
3. **Identify what changed:** check **Plugins** page for an "Update
   available" or "recently updated" plugin — that's your prime suspect.
4. **Binary search, don't disable everything at once:**
   - Deactivate the most-recently-updated plugin first and reload the
     site.
   - If that doesn't fix it, reactivate it, and instead deactivate the
     next most-likely suspect (anything in the Elementor/JetEngine
     family, since this site's design depends entirely on those).
   - Avoid deactivating **Elementor**, **Elementor Pro**, **JetEngine**,
     or **JetThemeCore** simultaneously with other testing — these four
     are foundational; if any of them is the actual problem, the fix is
     usually to roll back its version rather than leave it deactivated.
5. Once you find the conflicting plugin, you have three options, in order
   of preference:
   - **Roll back** that one plugin to its previous version (via
     **Plugins → [plugin] → View details → Advanced View** if available,
     or reinstall the older version manually) rather than leaving it off.
   - **Update the *other* side of the conflict** (e.g. if a JetEngine
     add-on conflicts with core JetEngine, make sure both are on their
     latest compatible versions together rather than one updated and one
     not).
   - **Leave it deactivated** only if it's not essential to any visible
     page feature.
6. Always test on the **live page that uses the feature**, not just the
   dashboard — some plugin conflicts only show up on the front end (e.g.
   broken Elementor widgets) not in wp-admin.

### "A specific section/widget looks wrong" (not the whole site)

This is usually a content/template issue, not a plugin conflict:

1. Identify whether the broken thing is a **repeated grid of cards** (→
   check the relevant Listing Template above) or a **one-off page
   element** (→ edit that specific page directly with Elementor).
2. Check Elementor's **History → Revisions** panel on the relevant
   page/template for a version to compare against or restore (note:
   revisions are pruned over time and may not go back far enough — see
   the staff-photos-fix.md writeup for an example of this limitation).
3. If it's an image specifically, check whether the field uses a
   **Dynamic Tag** (small icon next to "Choose Image"/"Choose File" etc.)
   — a missing/cleared dynamic tag is a common, easy-to-miss cause that
   looks like "the content disappeared" but is actually just a broken
   link to data that's still there.

### Before making any fix live

- Always check the fix in the Elementor **editor preview** before
  clicking **Publish**.
- After publishing, reload the **live page** with a cache-busting query
  string (e.g. `?cb=1`) to rule out stale page cache before concluding the
  fix didn't work.
