# Matching pilot: development verification

The pilot is an explicit `TNDMatchingPilot` Lake library target whose sources
live under `pilot/`. Its logical modules are `TNDLib.Matching.*`; it imports
only the existing finite-enumeration module and the bundled Lean core.
It is not imported by the approved `TNDLib.lean` root and is not part of the
existing production admission/export inventory. This separation keeps the
existing source guard effective while the new source grammar is reviewed.

The mathematical endpoint is:

```text
TNDLib.Matching.stableMatch (preferences : Preferences n) :
  { matching : PerfectMatching n // Stable preferences matching }
```

The actual program executes serial deferred acceptance. A generic proof shows
that `n * n + 1` units of interpreter fuel suffice, including the final
completion check. The resulting matching and its correctness proof cover
arbitrary valid input sizes, including zero. This is the selected finite
strict-complete stable-existence result; optimality, college quotas, ties,
incomplete preferences, and the whole paper are outside its scope.

## Reproduce

From the library root:

```sh
lake build TNDLib TNDMatchingPilot
lake env lean tests/matching/StableMatch.lean
python3 tests/matching/check_proposal.py
python3 tests/matching/check_pilot.py --revision FULL_40_CHARACTER_COMMIT_SHA
python3 tests/matching/check_downstream.py --revision FULL_40_CHARACTER_COMMIT_SHA
```

The exact-revision scripts require a committed candidate. They inspect its
production policy and reject validation-only activated flags. The downstream
script consumes that full Git SHA and its toolchain pin in a separate clean
Lake project, checks the resolved dependency graph, computes a size-three
matching, invokes the generic theorem, and checks the output with a separately
written finite blocking-pair predicate.

`check_pilot.py` clones the exact candidate locally without build products,
builds the original library and the pilot, runs all matching Lean test files,
exports the complete matching namespace, and replays it with the maintained
independent checker against `pilot-exports.json`. The manifest contains the
complete public generated and authored declaration inventory, not just the
displayed theorem names. Dependency auditing includes proof and statement
dependencies and rejects axioms outside `propext` and `Quot.sound`.

The same candidate export is supplied as both comparator inputs. This is a
library self-audit: kernel replay and dependency auditing are genuine, while
comparison to itself does not independently establish agreement with a separate
approved challenge statement. The separately reviewed
[statement map](../../docs/MATCHING-STATEMENT-MAP.md) explains the source-to-Lean
correspondence. A proof-task comparison against an independently approved
challenge is a different operation.

## Source-admission limitation

The unchanged production parser admits the seven-declaration plain-term
`Proposal` module in a supplemental fixed-inventory snapshot. The full pilot
uses a richer, still pure mathematical syntax, including explicit data
structures and inductive result types. Its direct admission probe is rejected
at `Lean.Parser.Command.structure`.

The full replay report records that rejection, keeps `development_only: true`,
and explicitly sets `production_certification: false`. It does not substitute
the seven-declaration success for full-pilot source admission. No production
flag, grammar allowlist, or approved production source inventory has been
expanded by these scripts.

[ADMISSION-REVIEW.md](ADMISSION-REVIEW.md) inventories the observed syntax,
proposes a restricted development profile and exact local import-DAG policy,
and lists the negative cases that must pass before the full source-admission
gate can be considered satisfied. That gate remains open.

## Evidence files

Generated reports and large exports live under ignored `audit/output/matching/`.
Their source/manifest/binary hashes and candidate SHA bind each report to its
actual inputs. These are local macOS development runs. They do not establish
native-Linux isolation, production resource-boundary validation, or publication
approval. Final review must bind the reports to the proposed candidate commit.
