# jUCMNavTest

## What is this project?

This is a **test suite** for **jUCMNav** — an Eclipse plugin that lets you draw and analyze requirement diagrams (specifically Feature Models, Use Case Maps, and Goal graphs).

This repo does NOT contain the jUCMNav app itself. It only contains the tests that check whether jUCMNav works correctly.

---

## What does it test?

The main thing being tested is the **Feature Model Strategy Algorithm** — the logic that decides whether a software feature is "selected" (active) or "not selected" (inactive) based on rules like:

- **Mandatory**: this feature must always be on
- **Optional**: this feature can be turned on or off
- **AND decomposition**: all child features must be on
- **OR decomposition**: at least one child feature must be on
- **XOR decomposition**: exactly one child feature can be on (mutually exclusive)

Features are scored as either `0` (not selected) or `100` (selected).

---

## Folder structure

```
jUCMNavTest/
├── test/
│   ├── blackBox/
│   │   └── FeatureModelStrategyAlgorithmTest.java   # 129 black-box tests
│   └── whiteBox/
│       └── AutoSelectMandatoryFeaturesTest.java     # 3 white-box tests
├── testData/
│   └── junit-workspace/
│       └── jUCMNavTest/
│           ├── blackboxtestcases/    # 44 .jucm diagram files for black-box tests
│           ├── whiteboxtestcases/    # 3 .jucm diagram files for white-box tests
│           └── userinterfacetestcases/  # 3 .jucm files for manual UI testing
└── readme.txt
```

---

## The two test classes

### 1. `FeatureModelStrategyAlgorithmTest` (Black-box)

Tests the overall algorithm from the outside — given a feature diagram and some selected features, does the algorithm output the right evaluation scores?

Each test:
1. Opens a `.jucm` file in the Eclipse editor
2. Creates a strategy and marks features as selected (`100`) or not selected (`0`)
3. Runs `autoSelectAllMandatoryFeatures` (auto-enables required features)
4. Checks every feature's final score against the expected value

### 2. `AutoSelectMandatoryFeaturesTest` (White-box)

Tests the `autoSelectAllMandatoryFeatures` method specifically — checks whether it correctly tags features with `METADATA_AUTO_SELECTED` (an internal marker that means "this was auto-turned on").

---

## How to run the tests

The tests run inside Eclipse as JUnit Plugin Tests (not regular JUnit). They need a live Eclipse workspace.

**Setup steps:**
1. Copy everything inside `testData/junit-workspace/` into your local Eclipse junit-workspace folder
2. In your Eclipse run configuration:
   - Uncheck **"Clear"** in the Main tab
   - Uncheck **"Clear the configuration area before launching"** in the Configuration tab
3. Set the execution environment to **JRE 1.7** with a **32-bit JVM**

---

## Known issues / limitations

- **Root features evaluate to 0** instead of 100 in some cases (known bug, not fixed)
- **No `autoSelectAllMandatoryParents`**: if you manually select a mandatory child, its parent is not auto-selected — this causes some tests to fail
- **OR-child bug**: in white-box test3, `OrChild1` doesn't get the auto-selected metadata when it should (documented as a known failure)

---

## Key classes from jUCMNav (not in this repo)

These are imported from the jUCMNav plugin:

| Class | What it does |
|---|---|
| `FeatureModelStrategyAlgorithm` | The main algorithm being tested |
| `EvaluationStrategyManager` | Manages which strategy is active |
| `UCMNavMultiPageEditor` | The Eclipse editor for `.jucm` files |
| `FeatureUtil` | Helper methods (e.g. find root features) |
| `MetadataHelper` | Read/write metadata on diagram elements |
