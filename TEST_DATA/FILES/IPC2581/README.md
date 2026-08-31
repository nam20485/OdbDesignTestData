# IPC-2581 Sample Files

Sample IPC-2581 (DPMX) XML files + XSD schemas for OdbDesign IPC-2581 import implementation,
validation, and testing. **Primary focus: revision C** (official IPC testcase suite).
Downloaded 2026-08-30; all XML verified well-formed with `xmllint --noout`.

The suite was trimmed for size (449 MB → 198 MB): redundant `assembly`/`fabrication`/`test`
variants were removed after verifying each board's `full` file is their exact union
(component/nets/layers counts match), and a byte-identical duplicate copy of testcase10
(from a second source repo) was dropped. The small `bom`/`stackup`/`stencil` mode files
were kept — they exercise missing-section tolerance at negligible size.

## Summary

**Revision-C suite — official IPC testcases (one dir per board):**

| Dir | Board | `*-full.xml` stats (nets L/P, comps, pkgs) | Full size | Also kept |
|---|---|---|---|---|
| `testcase1-revc/` | T1 network card | 2436 / 2436, 1656, 105 | 56 MB | bom, stackup, stencil |
| `testcase3-revc/` | T3 round test card | 261 / 261, 42, 8 | 2.9 MB | bom, stackup |
| `testcase5-revc/` | T5 | 1077 / 1077, 927, 63 | 28 MB | bom, stackup, stencil |
| `testcase6-revc/` | T6 | 1324 / 1324, 1273, 56 | 26 MB | bom, stackup, stencil |
| `testcase9-revc/` | T9 LED display card | 79 / 79, 60, 15 | 3.2 MB | bom, stackup, stencil |
| `testcase10-revc/` | T10 | 514 / 514, 56, 5 | 20 MB | bom |
| `testcase11-revc/` | T11 **rigid-flex** | 114 / 116, 78, 13 | 1.9 MB | bom, stackup, stencil |
| `testcase12-revc/` | T12 **rigid-flex** | 79 / 79, 60, 15 | 1.7 MB | bom, stackup, stencil |

**Other samples (flat files in this dir):**

| File | Declared rev | Nets (L/P) | Comps | Pkgs | Size | Notes |
|---|---|---|---|---|---|---|
| `beaglebone_black_revb6.xml` | B | 484 / 478 | 391 | 43 | 41 MB | Real board, Allegro export; only large rev-B board |
| `switch_board.xml` | B | none | 27 | 10 | 5.1 MB | Real board, geometry but **no netlist** |
| `led_power_board.xml` | C | 5 / 0 | 10 | 6 | 18 KB | Small real board; quick unit-test input |
| `DM0002-IPC-2518.xml` | C | none | 0 | 0 | 2 MB | Geometry-only; no components/nets |
| `ipc2581c_skeleton.xml` | C | — | 1 | 0 | 1.5 KB | Degenerate skeleton, empty attrs; robustness edge case |

**XSD schemas (`spec/`):**

| File | Revision |
|---|---|
| `IPC-2581A.xsd` | A |
| `IPC-2581B.xsd` | B |
| `IPC-2581B1.xsd` | B errata 1 |
| `IPC-2581C.xsd` | C (primary target) |

Total: ~198 MB, 34 XML files + 4 XSDs.

## Parser notes

- **Net elements:** every file — including all revision-C ones — uses `<LogicalNet>`/`<PhyNet>`.
  Verified against `spec/IPC-2581C.xsd`: revision C defines `LogicalNet`, `PhyNet`, `PhyNetPoint`,
  `PhyNetGroup`, `NetRef`, `NetKey`, `NetShort` — **there is no `<Net>` element**. Any design doc
  assuming `<Ecad><Data><Net>` needs correction.
- Root element is `<IPC-2581 revision="B|C">`, namespace `http://webstds.ipc.org/2581`.
- Function modes: the kept `bom`/`stackup`/`stencil` files each contain only their mode's
  sections — the importer must tolerate missing sections (see also `DM0002`, which has
  no components or nets at all).
- Rigid-flex coverage: `testcase11-revc`, `testcase12-revc`.
- Biggest inputs for memory/perf testing: `testcase1-revc-full.xml` (56 MB),
  `beaglebone_black_revb6.xml` (41 MB), `testcase5-revc-full.xml` (28 MB).

## Provenance

| Content | Source | Ref |
|---|---|---|
| Rev-C testcase suite (all dirs incl. `DM0002-IPC-2518.xml`) | [diodeinc/pcb](https://github.com/diodeinc/pcb), `crates/ipc2581/tests/data/` (zstd-compressed upstream) | `69edaa0eb70e` |
| XSDs in `spec/` | [hpcreery/IPC-2581_Serializer.NET](https://github.com/hpcreery/IPC-2581_Serializer.NET), `schemas/` | `b32c633e78c2` |
| `beaglebone_black_revb6.xml` | [sjgallagher2/ipc2581](https://github.com/sjgallagher2/ipc2581), `examples/` | `befdfb020e1d` |
| `switch_board.xml`, `led_power_board.xml` | [mgburr/ipc2581-to-kicad](https://github.com/mgburr/ipc2581-to-kicad), `samples/` (original names `SWITCH BOARD.xml`, `led_power_board.xml`) | `8d7144f67b98` |
| `ipc2581c_skeleton.xml` | [tottthy2/IPC2581Validator](https://github.com/tottthy2/IPC2581Validator), `Input/output1.xml` | `ab1798584285` |

sjgallagher2/ipc2581 also carried a `testcase10-Rev C data` set; it was byte-identical to
`testcase10-revc/` here and was dropped as a duplicate.

If more tool-diversity or revision-A fixtures are needed later:
[IntelligentElectron/test-fixtures](https://github.com/IntelligentElectron/test-fixtures) provides an
`ipc2581/download-fixtures.sh` that pulls official samples (Allegro, Zuken; rev A/B/C) from
`www.ipc2581.com` — note its NOTICE.md flags some fixtures as fair-use/no-license.
