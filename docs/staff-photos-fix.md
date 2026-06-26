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
