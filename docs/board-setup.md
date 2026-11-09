# HLD board setup (one-time, GitHub UI)

Model: **one issue per HLD** (the work item), **one Project board** (the view), **repo files** (source of truth).

1. Profile > Projects > New project > Board. Name: `HLD Pipeline`.
2. Workflows > "Auto-add to project": repo `EAOps`, filter `is:issue label:hld`.
3. Views:
   - **By stage**: Board, column field = Status (Ideation / BL review / VR / HLD submitted / Done / On hold).
   - **By business line**: Table, group by label `bl:*`.
   - **Blocked**: Table, filter `label:blocked`.
4. Project settings > Link a repository: `EAOps`.
5. Add the board URL to README.md.
