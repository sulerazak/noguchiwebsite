# Fix: Staff Photos Not Displaying on Animal Experimentation Page

**Site:** https://noguchi.ug.edu.gh/research-departments/animal-experimentation/
**Date:** 2026-06-26
**Affected sections:** "Research Scientists" and "Research Staff"

## Problem

After an edit to the page, staff/scientist profile photos stopped displaying.
Every card in the "Research Scientists" and "Research Staff" grids showed a
generic grey placeholder icon instead of the person's actual photo.

## Root Cause

The site uses **Elementor + JetEngine** (WordPress page builder + dynamic
content plugin). Each person's card is rendered from a single shared
**Listing Item Template** ("Fellow Listing Template", post ID 138) that
JetEngine repeats once per "Member" custom-post-type entry.

The Image widget inside that shared template had lost its **Dynamic Tag**
binding — instead of pulling each person's photo automatically via
`Post Thumbnail`, the "Choose Image" field had no source configured at all.
Because the template is shared across every card, this one broken binding
caused *every* card (not just one) to fall back to Elementor's default
placeholder graphic.

The underlying photo data itself was never lost — each Member post (e.g.
"Dr. Samuel Adjei", post ID 1015) still had its correct photo stored under
"Profile Image" / Featured Image. Only the *link* between the template and
that data was broken.

A second, unrelated issue was found and fixed during cleanup: the same
template contained a leftover/duplicate Image widget with no name or role
data attached to it. Once it was also bound to a photo (to test it), it
caused every card to render two stacked photos, making the grid taller and
overlapping the "Research Staff" heading below. This element was removed
entirely since it served no purpose.

## Solution

1. Opened **Elementor → JetEngine → Listings/Components → "Fellow Listing
   Template"** (`wp-admin/post.php?post=138&action=elementor`).
2. Selected the broken Image widget → **Dynamic Tags** icon → **JetEngine →
   Custom Image** → Field: **Post thumbnail**.
   (Equivalently, Elementor's built-in **Post → Featured Image** dynamic tag
   works too.)
3. Verified the photo rendered correctly in the editor preview, then
   **Published**.
4. Confirmed the "Research Staff" section's cards were already correctly
   bound in their own template ("Research Staff Listing Template", post ID
   161) — those photos had only appeared broken due to stale page cache;
   a fresh page load resolved them.
5. Found and removed the leftover duplicate Image widget/Section in the
   Fellow Listing Template that was causing a layout overlap with the
   "Research Staff" heading.
6. Re-published and verified the live page: all photos display once, in
   the correct position, with no overlap.

## How to Fix It Manually (Step-by-Step)

Use this walkthrough if staff photos go blank again. It assumes you're
logged into WordPress admin (`wp-admin`) as an admin/editor.

### Step 1: Confirm it's the placeholder-icon symptom

Visit the affected page (e.g.
`https://noguchi.ug.edu.gh/research-departments/animal-experimentation/`)
and check the photo grids. The bug looks like this:

- A generic grey box with a small "mountain/sun" icon shows instead of a
  real photo.
- The name and role text next to it are still correct.
- It usually affects **every person in the grid at once**, not just one
  person. If only one person's photo is missing, that's a different,
  simpler problem — see "If only one photo is missing" at the end.

### Step 2: Find which Listing Template is being used

Each card grid on a page is powered by one shared "Listing Item Template,"
not edited per-person. To find the right one:

1. Go to **wp-admin → JetEngine → Listings/Components**
   (`/wp-admin/edit.php?post_type=jet-engine`).
2. Find the template by name and "For post type/taxonomy" column. For this
   site:
   - **Research Scientists** section → **Fellow Listing Template**
   - **Research Staff** section → **Research Staff Listing Template**
   - (Other sections elsewhere on the site may use **Admin Staff Listing
     Template**, **Project Lead Listing Template**, etc. — match by the
     "For post type/taxonomy" column, which will say "Members".)
3. Hover the row and click **Edit with Elementor**.

### Step 3: Open the broken Image widget

1. In the Elementor editor, you'll see one example card rendered (e.g. one
   person's name/photo as a preview).
2. Click directly on the photo/placeholder image in that card.
   This opens the **Edit Image** panel on the left.

### Step 4: Check the Dynamic Tag binding

1. In the **Edit Image** panel, look at the **Choose Image** field.
2. Hover over it — a small **list/database icon** appears on the right
   side of the "Choose Image" button. This is the **Dynamic Tags** icon.
3. Click it. A dropdown appears with categories: Post, Site, Author,
   JetEngine, etc.
   - If a tag is already selected, it will show as a labeled chip (e.g.
     "Custom Image" or "Featured Image") at the top of the **Choose Image**
     field instead of an empty box. If this chip is **missing** and the box
     is just an empty/placeholder image, that confirms the bug.
4. Select a binding that pulls the photo automatically per person. Either
   of these works:
   - **Post → Featured Image** (simplest), or
   - **JetEngine → Custom Image**, then in the Settings that appear below
     it, set **Field** to **Post thumbnail**.
5. The preview in the canvas should immediately update to show a real
   photo instead of the placeholder.

### Step 5: Check for a second/duplicate image widget

Some templates on this site accidentally ended up with a second, unused
Image widget stacked below the name/role text (with no dynamic tag and no
name attached to it). If you see **two** photo-shaped elements in the
single-card preview:

1. Click the **Structure** icon in the top toolbar (looks like layered
   squares) to open the page structure tree.
2. Look for a second `Section > Column > Image` entry that has no
   Heading/Text widgets next to it (i.e. it doesn't show a name or role).
3. Right-click that Section in the tree → **Delete**.

If you're not sure whether an extra image is meant to be there, leave it
and ask before deleting — but a stray image with no associated name/role
text is almost certainly leftover clutter, not real content.

### Step 6: Publish and verify

1. Click **Publish** (top right of the Elementor editor).
2. Open the live page in a new tab with a cache-busting query string, e.g.
   add `?cb=1` to the end of the URL, to bypass any page cache:
   `https://noguchi.ug.edu.gh/research-departments/animal-experimentation/?cb=1`
3. Scroll to the affected section and confirm every person's photo now
   shows correctly, with no overlap or extra blank space pushing into the
   next heading.
4. If photos still look broken after publishing, it's likely browser/page
   cache — try a hard refresh (Cmd+Shift+R) or wait a minute and reload.

### If only one photo is missing (not the whole grid)

This is a different, simpler issue — the shared template is fine, but one
specific person's data is missing their photo:

1. Go to **wp-admin → Members → All Members**.
2. Find and open that person's entry.
3. Look at the **Profile Image** box in the right-hand sidebar (or the
   Featured Image box, depending on how it's labeled).
4. If empty, click into it and upload/select the correct photo, then
   **Update**.
5. No need to touch Elementor or any template for this case.

## Why This Happened

Dynamic Tag bindings in Elementor/JetEngine are easy to accidentally clear
while editing a widget (e.g. clicking the "x" next to the dynamic tag, or
replacing it with a manually-chosen static image and then removing that
image). Because the binding lives on the *shared template*, not on
individual pages or posts, a single accidental click can silently break
the photo for every person who uses that template — which is what made
this look like "files going missing" when in fact the data was never
touched.

## Prevention / Notes for Future Edits

- This page's photo grids are **not** edited per-person on the page itself.
  To change how photos display for *everyone*, edit the shared listing
  template (JetEngine → Listings/Components). To change *one* person's
  actual photo, edit that person's "Member" post and update its Featured
  Image / Profile Image.
- If photos go blank again after an edit, check the Image widget's
  "Choose Image" field for the small Dynamic Tags icon — if it shows no
  tag selected, that's the bug.
- Elementor revision history on the *page* only went back ~3 years
  (autosaves are pruned), so it was not useful for diagnosing a recent
  change. The listing template's own revision history was checked but also
  didn't capture the breaking change directly — diagnosis ultimately relied
  on inspecting the live DOM/network requests to confirm no image URL was
  even being requested, which pointed to a missing dynamic tag rather than
  a deleted file or 404.
