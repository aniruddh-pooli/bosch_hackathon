# BankResilience — Findings Report

> Generated from persisted local Docker experiment evidence and saved Gemini engineering reviews. Metrics are deterministic; AI recommendations are proposals for validation, not applied fixes.

## Scope and provenance

This report covers the latest recovered run of each of the three original experiments in the selected database. Packet loss, CPU stress, and combined experiments are separate additions. No new faults or payments are injected by this exporter.

**Overall System Resilience Score: 74.3 / 100** — coverage 3 / 3 original experiment types. Equal-weight mean of their latest completed primary-probe scores. This is not a production SLA or proof of transaction correctness.

## Experiment summary

| Experiment | Evidence ID | Baseline → fault p95 | Fault availability | Recovery time | Score | Diagnosis |
|---|---|---:|---:|---:|---:|---|
| LedgerWriter outage | `20061a38` | 290.80 → not measured ms | 0.00% | 20.02s | 69/100 | gemini |
| Network slowdown | `db093e1e` | 403.10 → 1884.40 ms | 100.00% | 4.63s | 85/100 | gemini |
| Contacts hang | `ea692ed5` | 345.90 → not measured ms | 0.00% | 3.99s | 69/100 | gemini |

All listed runs passed recovery verification. A missing fault p95 indicates there were no successful target probes; it does not indicate zero latency.

## Severity-ranked vulnerabilities

Ranking uses measured severity first, then lower score, then higher dependency criticality. Gemini narrative severity is shown separately where available.

### 1. HIGH — LedgerWriter outage

HIGH: 69/100; largest penalties: target errors (−20), recovery duration (−5.72). ledgerwriter can affect callers frontend; it depends on balancereader, ledger-db. Dependency criticality 7 breaks severity/score ties. Caller impact is potential unless measured.

Observed affected services: ledgerwriter. Evidence: `20061a38`, completed `2026-10-10T22:21:46.728+00:00`. Saved AI analysis severity: CRITICAL.

### 2. HIGH — Contacts hang

HIGH: 69/100; largest penalties: target errors (−20), mitigation delay (−5.83). contacts can affect callers frontend; it depends on accounts-db. Dependency criticality 6 breaks severity/score ties. Caller impact is potential unless measured.

Observed affected services: contacts. Evidence: `ea692ed5`, completed `2026-10-10T22:24:19.823+00:00`. Saved AI analysis severity: HIGH.

### 3. MEDIUM — Network slowdown

MEDIUM: 85/100; largest penalties: mitigation delay (−5.56), latency degradation (−4.08). ledgerwriter can affect callers frontend; it depends on balancereader, ledger-db. Dependency criticality 7 breaks severity/score ties. Caller impact is potential unless measured.

Observed affected services: ledgerwriter. Evidence: `db093e1e`, completed `2026-10-10T22:27:09.400+00:00`. Saved AI analysis severity: MEDIUM.

## AI-recommended remediation and verification

The following recommendations are extracted from saved Gemini engineering reviews, with their original evidence references. Existing configuration was not inspected unless explicitly stated. Controlled shutdowns and pauses were injected by the experiment; recommendations do not establish an unexplained production incident or the absence of existing controls.

### LedgerWriter outage — `20061a38`

- **HIGH: Evaluate automated restart policy** — If the orchestrator is configured with an appropriate restart policy, then the service will recover automatically from unexpected shutdowns. Validation: Verify orchestrator configuration for restart-on-failure policies. Evidence: target-logs, observation-1. Configuration: not-inspected.
- **MEDIUM: Evaluate shutdown signal handling** — Whether the service is receiving unexpected SIGTERM signals, then investigating the source of the shutdown signal will prevent future outages. Validation: Review system and application logs for external signals triggering the shutdown hook. Evidence: target-logs. Configuration: not-inspected.

### Network slowdown — `db093e1e`

- **MEDIUM: Evaluate automated network anomaly detection** — If automated alerts for high-latency network policies are implemented, then response time to injected impairments will decrease. Validation: Monitor alert triggers during future network-related experiments. Evidence: network-policy, observation-1. Configuration: not-inspected.

### Contacts hang — `ea692ed5`

- **HIGH: Evaluate automated process health monitoring** — If automated monitoring detects paused processes, then the system can trigger alerts or auto-unpause to reduce MTTR. Validation: Verify that alerts trigger when a container enters a paused state. Evidence: observation-1, observation-2. Configuration: not-inspected.

## Measurement limits and next actions

- Only observed services are counted as affected; declared caller dependencies indicate potential impact.
- Readiness and public-page success do not establish successful authenticated transfers or ledger integrity.
- Saved AI suggestions must be checked against current standby protection and routing configuration before implementation.
- Run the five-fault suite for broader coverage, and the combination suite to measure overlapping network/resource pressure. Compare against single-fault runs; differing baseline conditions prevent strict causal attribution.
- Independently verify transfer IDs, balances, transaction history, and duplicate prevention before claiming transaction correctness.
