# Preview fixtures for issues #75 and #79

Created on 2026-10-20 using Preview 11.0 on macOS 27.0.1 (26A434).
Operator: Codex through native computer control, explicitly requested by the user.
These are actual Preview saves, not programmatically generated substitutes.
They are agent-operated observations, not a human sign-off.

Monight was the installed `/Applications/Monight.app`, version 0.6.0. Its executable
SHA-256 is recorded in `manifest.json`. Its source revision and CI artifact provenance
were not established. Do not attribute these results to the historical release candidate
or use them to claim a verified candidate pass.

## Inputs for #79

`manifest.json` registers external readers in `readers` and inventories the two canonical
Preview inputs in `files`. Each inventory entry contains its SHA-256, size, metadata, outline, page text,
decoded page-content checksum, page geometry, and native Annotation values. Annotation
rectangles and QuadPoints use PDF points, with zero-based page indexes in the inventory.
Select externally authored objects by subtype, contents, and author, rather than assuming
Preview supplied a stable `/NM`. Preview's author value is retained as written by Preview.

| File | Observed Monight classification | Relevant objects |
| --- | --- | --- |
| `preview-supported.pdf` | Editable | Preview-authored Highlight and Text, original Guest Text, two original Links, and Popup objects |
| `preview-unsupported.pdf` | Read-only: `Unsupported embedded annotations; saving is blocked` | Same supported objects plus one Preview Rectangle (`/Square`) |

The 0.01-point tolerance in the registration is an intended serialization tolerance,
not permission to accept the observed 4-point shrink of the Preview note. #79 should
expose that failure rather than change the expected input geometry to the broken output.
An Acrobat fixture can be registered later with another `readers` entry and inventory
entries; none was created or tested in this session. The automated #79 contracts have
not been implemented by this fixture-preparation change.

## Preview provenance

1. Copied `../native-annotations/annotated.pdf` to a writable working directory. Opened
   it in Preview and selected Highlights and Notes. Preview showed Guest highlight,
   Guest comment, and Ada Lovelace comment as native objects.
2. Opened the highlight's popup and changed its contents to
   `Preview edited Monight highlight`. Tried opening/editing the original Guest note;
   the editor did not open and its text remained `Native comment`. This is a failure,
   not a completed edit.
3. Removed the Ada Lovelace note through Preview's Remove Note action.
4. Added a native Preview note containing `Preview native note 75`.
5. Selected the lower text and used Preview's annotation style controls. An intermediate
   Underline was removed. The resulting yellow Highlight spans both the first and lower
   text lines and is authored by Preview; its contents were changed using the popup to
   `Preview native highlight 75`. The final file contains no Underline annotation.
6. Saved in Preview and preserved the bytes as `preview-supported.pdf`, before any
   subsequent Monight edits. This includes Preview's rewrite/combination of the initial
   highlight; do not describe it as two independent surviving highlights.
7. Copied that file, opened the copy in Preview, added a Rectangle through Shapes,
   acknowledged the non-redaction notice, and saved as `preview-unsupported.pdf`.
8. Opened both files in installed Monight. The supported file allowed editing; the
   unsupported file displayed its explicit explanation and disabled Save and Save As.

## Supporting round-trip evidence (outside Git)

The seven intermediate PDFs below are retained under the repository-relative archive
`artifacts/issue-75/2026-10-20/`, which is ignored by Git. The same archive is synced to
the remote project. Its separate `manifest.json` includes all nine PDFs, and `rendered/`
contains visual QA images. These files document one installed-build session; #79 should
generate new exports from the canonical inputs rather than treat these outputs as goldens.

| Evidence file | Actual operations |
| --- | --- |
| `evidence/monight-resaved.pdf` | Opened the supported input in Monight; edited Preview note and highlight contents to `Monight re-edited Preview note 75` and `Monight re-edited Preview highlight 75`; deleted the original Guest comment; saved. |
| `evidence/preview-second-save.pdf` | Opened that Monight export in Preview; edited highlight to `Preview second edit highlight 75`; note text editor did not open; removed note using Remove Note; saved. |
| `evidence/saved-under-transforms.pdf` | Save As in Monight with 150% zoom, 90-degree clockwise viewing rotation, and Sepia active; Preview showed upright, unfiltered pages and native annotations. |
| `evidence/monight-created.pdf` | Opened a copy of native `original.pdf` in installed Monight; created highlight and text comment with default Guest author and contents `Monight Guest highlight 75` and `Monight Guest note 75`; saved. |
| `evidence/preview-edited-guest.pdf` | Opened those Guest objects in Preview; edited highlight to `Preview edited Guest highlight 75`; note editor did not open; deleted note; saved. |
| `evidence/monight-second-save.pdf` | Reopened the Preview-returned Guest file in Monight under a separate working path; confirmed one comment and deleted note absent; re-edited highlight to `Monight second edit Guest highlight 75`; saved. |
| `evidence/preview-deleted-all.pdf` | Opened the second Monight save in Preview; opened highlight editor, changed its contents again, then removed Highlight and saved. Only Links and residual Popup objects remain; “all” refers to visible highlight/note markup, not every `/Annot`. |

The two copies used for repeated hand-offs keep original and intermediate evidence
separate and avoid testing against an already-open stale document. All existing input
PDFs in `native-annotations/` were left unchanged.

## Failures and limits

- Preview displayed/selects/deletes Monight notes but did not open their text editor.
  The same happened to the newly created Guest note. A Preview-authored note's editor
  opened before Monight re-save and did not open after that save. Root cause is unproven.
- Native inspection also found that Monight re-save changed the Preview note's Rect
  from `[220.6667, 372.0833, 244.6667, 396.0833]` (24 by 24 points) to
  `[220.6667, 372.08331, 240.6667, 392.08331]` (20 by 20 points).
- Print button and Cmd+P on the transformed supported file produced no native print
  panel. No printed PDF exists; printed-transform verification could not be completed.
- No additional Monight creation tool was enabled. Preview's Rectangle is intentionally
  unsupported input. Preview round-trip acceptance remains failed, and #75 stays open.

The detailed verification record and ready-to-file failure reports are in
[`docs/issue-75-preview-verification.md`](../../../../docs/issue-75-preview-verification.md).
