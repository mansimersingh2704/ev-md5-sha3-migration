# Agents Workspace Rule

## Hard Constraints
1. Defensive research only. Every attack demo runs ONLY against a local simulator (simulated charge point + simulated CSMS). No code that targets real stations, networks, vendors, or hosts.
2. Collision demos use pre-generated public MD5 collision fixtures produced by documented academic tools (fastcoll for identical-prefix, hashclash for chosen-prefix), stored as test vectors. Do NOT write new cryptanalysis or weaponized exploit code.
3. Be technically honest: OCPP 1.6 core does not mandate MD5. Define an explicit, documented "Legacy Profile" (MD5 checksum in firmware-update metadata, DataTransfer payload checksums, legacy token digests) and label it SIMULATED in code, UI and docs. Never present it as the OCPP standard.
4. New security functions use SHA-3 only (hashlib sha3_256 default, sha3_512 optional). Never use MD5/SHA-1 for new security functions. Never hand-roll crypto. A bare hash gives integrity only; use HMAC-SHA3-256 with per-station keys where authenticity is required. Use canonical JSON serialization before hashing and constant-time comparison (hmac.compare_digest).
5. Backward compatibility is the core requirement. The legacy parser module is FROZEN: never modify it. The MD5 field stays untouched; SHA-3 data goes in an extension location legacy parsers ignore (in-band sibling key for tolerant schemas, sidecar IntegrityAttestation for strict schemas). Prove this with tests that feed wrapped messages into the unmodified legacy parser (both tolerant and strict modes).
6. Backend owns all crypto, risk scoring and migration logic. Frontend only presents.
7. Data honesty: every displayed value carries provenance (source, timestamp, quality, truth_type, confidence) and a visible badge: [SIMULATED] [PUBLIC-TEST-VECTOR] [MEASURED-BENCHMARK] [SCENARIO]. Never present simulated data as live telemetry.
8. No secrets in code; config via env vars; ship .env.example.
9. No unnecessary infrastructure (no Kafka, Redis, Neo4j, microservices) unless instructed.
10. Preserve working features; never break existing routes or builds.
11. If any requirement conflicts with these rules, STOP and ask. Do not improvise.
12. Capability/migration state is stored server-side and never derived from message content.
13. SHA3_ENFORCED must require HMAC-SHA3-256 with per-station keys.
14. The `backend/app/legacy_frozen/` directory is strictly locked. A CI check must ensure it never changes, enforced by CODEOWNERS and a SHA3 lockfile.

## Workflow (Every Task)
1. Read AGENTS.md, then tasks/CURRENT_TASK.md. 
2. Read only 2-4 relevant docs; inspect existing code/tests first. 
3. Plan the smallest coherent change. 
4. Implement with strict scope.
5. Run backend pytest, frontend lint + build, and Playwright E2E. 
6. Update docs if contracts changed. 
7. Converge against acceptance criteria using tasks/CONVERGENCE_TEMPLATE.md.
8. Stop at the task's Stop Condition and report.

## Task Format
Task / Context / Objective / Relevant Docs / In Scope / Out of Scope / Implementation Requirements / Acceptance Criteria / Verification / Stop Condition.

## Report Format
Completed / Files Changed / Verification / Browser Verification / Known Limitations / Documentation Updated / Convergence Status (CONVERGED or NEEDS FIX) / Recommended Next Task.
