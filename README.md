# DQ-Sentinel — A Configurable Data Quality Governance Platform

> A lightweight, factory-pattern data quality engine that dynamically applies rules to any dataset via config — no hardcoded checks, no vendor lock-in. Built as a hands-on alternative to tools like Great Expectations, to demonstrate the underlying engineering rather than just tool configuration.

---

## Table of Contents
- [Overview](#overview)
- [Why This Exists](#why-this-exists)
- [Architecture](#architecture)
- [Data Quality Dimensions Covered](#data-quality-dimensions-covered)
- [How Configuration Works](#how-configuration-works)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Dashboard](#dashboard)
- [Sample Run / Demo](#sample-run--demo)
- [Comparison to Existing Tools](#comparison-to-existing-tools)
- [Roadmap](#roadmap)
- [License](#license)

---

## Overview
<!-- TODO: 3-4 sentences. What it does, who it's for, the one-line pitch. -->

DQ-Sentinel is a pluggable data quality governance engine. Point it at any dataset via a config file, and it dynamically applies the rules you've defined — no code changes required to add a new check to a new column. Results are stored for historical tracking and visualized on a dashboard.

## Why This Exists
<!-- TODO: The narrative — built from banking/FinTech ETL experience, the gap this fills, why config-driven > hardcoded checks. -->

- Most one-off DQ scripts are hardcoded to one dataset and one set of rules.
- Enterprise tools (Great Expectations, Talend, Informatica) work well but abstract away the underlying mechanics — this project exists to demonstrate that mechanics hands-on: factory pattern, dynamic dispatch, SOLID design.
- Built from real banking/FinTech ETL pain points around data reliability before it reaches downstream systems.

## Architecture
<!-- TODO: Insert architecture diagram here once built. -->

```
Config (YAML) → DQ Factory (dynamic class/method resolution) → DQ Engine → Results Store (MongoDB) → Dashboard (Plotly)
```

**Core design principles:**
- **Factory pattern** — rule classes self-register; the engine never hardcodes a class name.
- **Zero hardcoding** — column, rule class, and method are all resolved dynamically from config via `getattr`.
- **SOLID** — each dimension is a single-responsibility class; new dimensions plug in with zero impact on existing ones (Open/Closed).
- **Config-driven at every layer** — which columns, which classes, which rules, all externalized.

## Data Quality Dimensions Covered
<!-- TODO: Check off / expand as implemented -->

| Dimension | Description | Status |
|---|---|---|
| Completeness | Missing values, null checks | ⬜ |
| Uniqueness | Duplicate detection | ⬜ |
| Validity | Format/range/enum conformance | ⬜ |
| Accuracy | Value correctness against expected type/pattern | ⬜ |
| Timeliness | Data freshness / latency vs SLA | ⬜ |
| Integrity | Referential integrity across tables | ⬜ |
| Consistency | Cross-field / cross-table agreement | ⬜ |
| Volume Anomaly | Sudden surge/drop/average volume vs historical baseline | ⬜ |
| Schema Drift | Column added/removed/type-changed vs previous run | ⬜ |

## How Configuration Works
<!-- TODO: Replace with final config schema once locked -->

```yaml
column_name:
  DQClassName:
    method: rule_method_name
    params: { key: value }
```

Example:
```yaml
price:
  Validity:
    method: isInRange
    params: { min_val: 0, max_val: 1000000 }
```

## Tech Stack
<!-- TODO: Confirm final list -->

- **Python** — core engine
- **MongoDB** — source data + results storage
- **Plotly** — dashboard visualizations
- **Docker** — packaging for "plug and play" use by anyone
- **PySpark** *(optional, for scale)*

## Project Structure
<!-- TODO: Update as repo takes shape -->

```
dq-sentinel/
  base.py              # DQRuleBase, scope definitions
  factory.py           # DQFactory
  engine.py            # DQEngine (config-driven dispatch)
  rules/
    completeness.py
    accuracy.py
    uniqueness.py
    validity.py
    timeliness.py
    integrity.py
    consistency.py
    volume_anomaly.py
  datastore/
    mongo_store.py
  dashboard/
    app.py
  config/
    rules_config.yaml
  data/
    sample_dataset.csv   # with intentionally injected defects, for demo
  README.md
```

## Getting Started
<!-- TODO: Fill in once packaged -->

```bash
# clone
git clone <repo-url>
cd dq-sentinel

# install
pip install -r requirements.txt

# configure
# edit config/rules_config.yaml

# run
python engine.py --config config/rules_config.yaml
```

## Dashboard
<!-- TODO: Screenshots/GIF once built -->

- Radar chart: DQ score snapshot across dimensions for the latest run
- Trend lines: how each dimension's score moves over time
- (Optional) drill-down table of failing records

## Sample Run / Demo
<!-- TODO: Once synthetic dataset + injected defects are built, describe the "before/after" catch here -->

A sample dataset with deliberately injected defects (nulls, out-of-range values, duplicate IDs, a volume spike) is included to demonstrate the engine catching real issues, not just running clean.

## Comparison to Existing Tools
<!-- TODO: Fill in briefly — positions this as informed, not reinventing the wheel unknowingly -->

| | DQ-Sentinel | Great Expectations |
|---|---|---|
| Rule definition | Config-driven, dynamic class/method dispatch | Expectation suites |
| Extensibility | New rule class = zero changes to engine (factory + SOLID) | Custom expectations supported |
| Dashboard | Built-in (Plotly) | Requires separate Data Docs/integration |
| Scale | Pandas/PySpark | Pandas/Spark/SQL backends |

## Roadmap

**Phase 1 — Core (MVP)**
- [ ] Config-driven engine: dynamically resolves column → rule class → method, no hardcoding
- [ ] MongoDB integration (fetch source data, store results)
- [ ] Plotly dashboard (v1: radar chart of dimension scores)
- [ ] Multi-source/multi-venue support — plain Python runner looping over configured sources
- [ ] Synthetic dataset with injected defects, for demo

**Phase 2 — Orchestration & Scale**
- [ ] Apache Airflow DAG for scheduled, per-venue orchestration (replaces the plain Python loop)
- [ ] Docker packaging for "plug and play" use
- [ ] Streamlit wrapper for an interactive dashboard

**Phase 3 — Stretch**
- [ ] Schema drift detection
- [ ] Cross-venue clock synchronization check
- [ ] Row/group-scope checks (order lifecycle integrity, sequence gap detection)

## License
<!-- TODO -->