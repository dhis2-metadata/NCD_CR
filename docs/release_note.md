# Release Note { #ncd-cr-release-note }

## 1.0.0

Initial release of the Cancer Registry package

## 1.0.1

Patch release. Resolves a server-side crash (`StackOverflowError`) that prevented
records from being saved, and cleans up the program-rule set. **No edit-check logic
was changed** — every check produces the same result it did in 1.0.0; the changes are
structural and corrective only.

### Fixed - Saving a record could fail with HTTP 500 / `java.lang.StackOverflowError`

Several program-rule conditions were written as long flat boolean chains (`a || b || c || …`, up to 615 terms). The DHIS2 expression engine evaluates such conditions recursively — one batch of stack frames per operator — so the deepest
  chains exhausted the JVM thread stack during server-side rule evaluation on save
  (`org.hisp.dhis.lib.expression.eval.Calculator`).

  The 22 affected conditions were re-parenthesised into **balanced** expression trees.
  This reduces evaluation depth from linear to logarithmic (worst case: a 615-term
  chain went from depth ~615 to ~12) without changing the operands, operators,
  actions, priorities, or UIDs of any rule.

  | Metric | 1.0.0 | 1.0.1 |
  |---|---|---|
  | Longest flat boolean chain | 615 terms | 1 |
  | Max evaluation depth (rewritten rules) | ~615 | 17 |
  | Approx. JVM stack frames (worst rule) | ~3,700 | ~72 |

### Changed / Removed

**Program-rule set cleanup.** The legacy program-rule set (239 program rules) is
  removed as part of this release; the corrected rules ship in the 1.0.1 metadata.
  A ready-to-run delete payload is included (`delete_program_rules.json`,
  `importStrategy=DELETE`). Deleting a program rule cascades to its program-rule
  actions automatically.

### Affected program rules (22 rebalanced)

| UID | Program rule |
|---|---|
| `pPErjbooDVp` | CR - Assign the value "+" to Topography Morphology key |
| `KnAi5ItRorR` | CR - Assign the value "-" to Topography Morphology key |
| `UzlMHkXXZ1i` | CR - Assign the value "*" to Topography Morphology key |
| `IzlGHEV4SZ7` | CR - Check grade |
| `C52NUGggKhW` | CR - Rare basis: check not passed |
| `PUUhj3Vy6Z9` | CR - Rare Age Topography Morphology |
| `PYNY6GAbj5k` | CR - Assign value 'Hematopoietic, lymphoid tissue: Unspecified types' to morphology group |
| `kDGhdaT84AF` | CR - Morphology family: Anal tumours |
| `ClGHCk8bAez` | CR - Morphology family: Haematopoietic tumours |
| `PVZAiKL9CXi` | CR - Morphology family: NA |
| `RmqCsfQP1z7` | CR - Morphology family: Skin tumours |
| `VwlbG6qekZ6` | CR - Morphology family: Stomach tumours |
| `RVawS91GVI7` | CR - Topography Morphology: MUST - Morphology family 01-11 |
| `IyIFLIQHrAt` | CR - Topography Morphology: MUST - Morphology family 12-20 |
| `dqJ41cxWpsL` | CR - Topography Morphology: MUST - Morphology family 21-28 |
| `bvJuGgzsPvI` | CR - Topography Morphology: MUST - Morphology family 29-44 |
| `MRwy5x76QDS` | CR - Topography Morphology: MUST - Morphology family 45-49 |
| `qKA56iP1W22` | CR - Topography Morphology: MUST - Morphology family 50-53 |
| `kLy40xQQlQ5` | CR - Topography Morphology: MUST - Morphology family 54-56 |
| `lxtvlQAVCC4` | CR - Topography Morphology: MUST - Morphology family 57-59 |
| `jGhcMVHK7T5` | CR - Topography Morphology: MUST - Morphology family 60-63 |
| `FHhE9prqA5v` | CR - Topography Morphology: MUST NOT |

### Notes & recommendations

- **Backward compatibility:** edit-check behaviour is unchanged; existing records and
  reports are unaffected by the rebalancing.
- **Operational backstop:** consider raising the DHIS2 Tomcat thread stack
  (e.g. `-Xss4m`) so future long-chain conditions have headroom.
