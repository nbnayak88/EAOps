# Anonymisation rules

This repository must be safe to read by anyone. Apply these before every write.

## Never include
- Person names: assignees, architects, reviewers, business owners, stakeholders, authors.
- Client or organisation names, including in titles, file names, commit messages and notes.
- Source-system identifiers that can be traced to a person or a client (user handles, emails, tenant URLs, query paths).
- Screenshots or raw exports. Keep only the extracted, anonymised rows.

## Always do
- Refer to people by function only when needed: "business owner", "security architecture", "review board".
- Refer to the organisation only as "Client".
- Identify each use case by its name and a neutral tracker ID (`UC-01`).

## Curating use-case names
If a title carries an identifiable term (a site, plant, entity, programme or product code that points at a specific client), replace that term with a generic equivalent and store the curated name.

| Source pattern | Curated form |
|---|---|
| Title ending in a site-specific or asset-specific qualifier | Drop the qualifier: "AI for Employee Assistant - Phase 1" |
| Client or entity name in a title | Remove it |
| Internal acronym that spells out a client name | Use a generic functional name |

Generic functional names (Supply Chain Control Tower, Key Control Testing, New GRC System) are fine as they are.

## Pre-push check
Run from the repo root and fix anything it returns:

```
grep -rniE "assigned|@[a-z0-9.-]+\.[a-z]{2,}" notes tracker
```

Then read the diff once for any name that slipped through. Add newly discovered sensitive terms to a private local blocklist, not to this file.
