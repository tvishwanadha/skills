# Creating ADRs

Step-by-step procedure for creating, superseding, and amending ADRs using file tools (no external dependencies).

> **Immutability rule**: Never edit the content of an accepted ADR. To change a decision, create a new ADR that supersedes or amends the original. Only the old ADR's Status line is updated — all other content is permanent.
>
> **Proposed ADRs are mutable.** If a Proposed ADR needs changes, edit it directly. If it's no longer relevant, delete it or set its status to Deprecated. The supersede/amend workflow only applies to Accepted ADRs.

## Procedure

### 1. Detect ADR directory

Default location: `docs/adr/`. If it doesn't exist, Glob for `**/adr/*.md` (respect `.gitignore`).

- If multiple candidate directories are found, ask the user which to use.
- If no ADR directory exists, ask the user where to create it. Then initialize it: read [`../assets/init.md`](../assets/init.md), replace `{DATE}` with today's date (ISO `YYYY-MM-DD`), and write it as `0001-record-architecture-decisions.md`. This canonical first ADR records the decision to use ADRs.

### 2. Determine next number

In the directory selected in step 1, Glob existing ADR files and parse their numeric prefixes (ignore files without a leading numeric prefix like `README.md`):

- Collect all numeric prefixes, take the **maximum**, and increment by 1.
- Match the existing numbering width (e.g., if files use `001-`, continue with 3-digit padding).
- If no ADRs exist yet, default to 4-digit padding (`0001`).

### 3. Generate filename

Format: `NNNN-title-in-kebab-case.md`

Slugify the title:
- Lowercase
- ASCII only (strip or transliterate non-ASCII)
- Replace non-alphanumeric characters with `-`
- Collapse consecutive hyphens
- Trim leading and trailing `-`

### 4. Write the ADR

1. Read the template at [`../assets/nygard-template.md`](../assets/nygard-template.md)
2. Replace placeholders: `{NUMBER}` → bare integer (e.g., `1` not `0001` — padding is only for filenames), `{TITLE}` → title, `{DATE}` → today's date (ISO `YYYY-MM-DD`)
3. Verify no `{...}` placeholders remain in the output
4. Write the file to the ADR directory

### 5. Supersede an existing ADR

The old ADR **must** have status `Accepted` before it can be superseded. If it is already Superseded or Deprecated, create a new ADR referencing the latest accepted record in the chain instead.

1. Create a new ADR following steps 1–4. Mention the prior ADR in the Context section (e.g., "This supersedes ADR-N because…").
2. Set the new ADR's Status section to:
   ```
   Proposed
   Supersedes [ADR-NNNN](NNNN-old-title.md)
   ```
3. Edit **only** the old ADR's Status section — replace the entire status with:
   ```
   Superseded by [ADR-NNNN](NNNN-new-title.md)
   ```
4. **Do not modify any other content in the old ADR.** Accepted ADRs are immutable historical records — only the Status section is ever updated.

### 6. Amend an existing ADR

The old ADR **must** have status `Accepted` before it can be amended. If it is already Superseded or Deprecated, do not amend it — create a new ADR referencing the latest accepted record instead. An ADR can have multiple amendments; each adds another "Amended by" line to the Status section.

1. Create a new ADR following steps 1–4. Mention the prior ADR in the Context section (e.g., "This amends ADR-N to clarify…").
2. Set the new ADR's Status section to:
   ```
   Proposed
   Amends [ADR-NNNN](NNNN-old-title.md)
   ```
3. Edit **only** the old ADR's Status section — keep the existing status and append a new line:
   ```
   Accepted
   Amended by [ADR-NNNN](NNNN-new-title.md)
   ```
   If there are already "Amended by" lines, add the new one below them.
4. **Do not modify any other content in the old ADR.** Accepted ADRs are immutable historical records — only the Status section is ever updated.

---

## Status Transitions

- **Proposed** → **Accepted**: After review and approval
- **Proposed** → **Deprecated** or deleted: If the decision is rejected or no longer relevant (Proposed ADRs are mutable — just edit or remove them)
- **Accepted** → **Superseded by ADR-N**: When replaced by a newer decision (status is replaced)
- **Accepted** → **Deprecated**: When no longer relevant
- **Accepted** + **Amended by ADR-N**: When clarified or extended (status stays Accepted, amendment note added below)

### Supersede vs Amend

- **Supersede**: The new decision *replaces* the old entirely — the original approach is no longer valid.
- **Amend**: The new decision *clarifies or extends* the original without replacing it — the core decision still holds.
