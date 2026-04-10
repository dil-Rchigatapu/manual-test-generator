# Memory Files — Schema Reference

> These files give the Planner, Critic, Explorer, and Generator agents persistent state across sessions. Update them as each agent completes its work.

---

## `coverage.json`

Tracks per-module coverage metrics. Updated by: Explorer (after exploration), Generator (after test generation), Planner (after plan generation).

```json
{
  "last_updated": "YYYY-MM-DD",
  "modules": {
    "<module-name>": {
      "explored": true|false,
      "roles_explored": ["MODERNIZED_SYSTEM_ADMIN_USEREMAIL", "..."],
      "last_explored_date": "YYYY-MM-DD",
      "context_file": "page-contexts/<file>.context.md",
      "flows_documented": 0,
      "tc_count": 0,
      "types_covered": ["E2E", "Functional", "Security"],
      "last_generated_date": "YYYY-MM-DD",
      "csv_files": ["manual-tests/<module>/<submodule>/<file>-test-cases.csv"],
      "last_critique_date": "YYYY-MM-DD",
      "critique_gaps_found": 0
    }
  }
}
```

---

## `risk-map.json`

Tracks high-risk zones, known bugs, and fragile areas. Updated by: Explorer (bugs found), Critic (critique gaps), Planner (risk scoring).

```json
{
  "last_updated": "YYYY-MM-DD",
  "high_risk_modules": ["<module>", "..."],
  "modules": {
    "<module-name>": {
      "risk_score": 0,
      "risk_factors": {
        "business_criticality": 1,
        "defect_probability": 1,
        "coverage_gap": 3,
        "role_sensitivity": 2,
        "recency_of_change": 1,
        "known_bugs": 0
      },
      "known_bugs": [
        {
          "id": "BUG-001",
          "type": "Functional|Accessibility|Visual",
          "element": "<element description>",
          "description": "<what the bug is>",
          "severity": "High|Medium|Low",
          "found_on": "YYYY-MM-DD",
          "status": "Open|Fixed"
        }
      ],
      "fragile_flows": ["<flow name>", "..."],
      "notes": "<any freeform notes>"
    }
  }
}
```

---

## `test-history.json`

Append-only log of all test generation runs. Updated by: Generator after each run.

```json
{
  "runs": [
    {
      "date": "YYYY-MM-DD",
      "module": "<module>",
      "submodule": "<submodule>",
      "mode": "Full|Smoke|Regression|Security|Accessibility|Negative",
      "context_file": "page-contexts/<file>.context.md",
      "tcs_generated": 0,
      "key_range": "BEW-TXXXX to BEW-TXXXX",
      "heuristics_triggered": ["Form with required fields", "Role-gated UI element"],
      "notes": ""
    }
  ]
}
```

---

## Update Rules

| Agent | When to Update | Which Files |
|---|---|---|
| Explorer | After saving a context file | `coverage.json` (explored=true, flows_documented), `risk-map.json` (if bugs found) |
| Generator | After writing CSV | `coverage.json` (tc_count, types_covered), `test-history.json` (new run entry) |
| Critic | After writing critique report | `coverage.json` (critique_gaps_found), `risk-map.json` (critical gaps as risks) |
| Planner | After producing the plan | `coverage.json` (risk scores updated), `risk-map.json` (new risk areas flagged) |
