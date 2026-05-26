# jUCMNavTest

Test suite for [jUCMNav](https://github.com/JUCMNAV/projetseg) — an Eclipse plugin for drawing and analyzing requirement diagrams.

> **Note:** This repo contains **only the tests**, not the jUCMNav application itself.

---

## What's being tested?

The main target is the **Feature Model Strategy Algorithm** — the logic that figures out which software features are "on" or "off" based on a diagram.

Each feature gets a score of either **0** (not selected) or **100** (selected), and the algorithm applies these rules:

| Rule | Meaning |
|---|---|
| **Mandatory** | This feature is always on |
| **Optional** | This feature can be on or off |
| **AND** | All child features must be on |
| **OR** | At least one child feature must be on |
| **XOR** | Exactly one child feature can be on |

---

## Project structure

```
jUCMNavTest/
├── test/
│   ├── blackBox/
│   │   └── FeatureModelStrategyAlgorithmTest.java   # 129 tests
│   └── whiteBox/
│       └── AutoSelectMandatoryFeaturesTest.java     # 3 tests
└── testData/
    └── junit-workspace/
        └── jUCMNavTest/
            ├── blackboxtestcases/       # 44 .jucm diagram files
            ├── whiteboxtestcases/       # 3 .jucm diagram files
            └── userinterfacetestcases/  # 3 .jucm files for manual UI testing
```

---

## The tests

### Black-box tests (`FeatureModelStrategyAlgorithmTest`)

Tests the algorithm end-to-end — given a diagram and a set of selected features, does the algorithm return the right scores?

Each test:
1. Opens a `.jucm` diagram file in Eclipse
2. Marks features as selected (`100`) or not selected (`0`)
3. Runs `autoSelectAllMandatoryFeatures` to auto-enable required features
4. Checks that every feature's final score matches what's expected

### White-box tests (`AutoSelectMandatoryFeaturesTest`)

Tests the `autoSelectAllMandatoryFeatures` method directly — specifically whether it correctly stamps features with the `METADATA_AUTO_SELECTED` marker.

---

## Setup & running

These are **JUnit Plugin Tests** — they run inside a live Eclipse instance, not standalone.

**Steps:**

1. Copy the contents of `testData/junit-workspace/` into your local Eclipse junit-workspace folder
2. Open your Eclipse run configuration and:
   - Uncheck **"Clear"** in the Main tab
   - Uncheck **"Clear the configuration area before launching"** in the Configuration tab
3. Set the execution environment to **JRE 1.7** with a **32-bit JVM**
4. Run as **JUnit Plugin Test**

---

## Known issues

- **Root features** sometimes evaluate to `0` instead of `100` (known bug, not fixed)
- **No auto-select for parents** — if you manually select a mandatory child, its parent won't be auto-selected, which can cause test failures
- **OR-child bug** — in white-box test 3, `OrChild1` doesn't get the `METADATA_AUTO_SELECTED` tag when it should (documented as a known failure)
