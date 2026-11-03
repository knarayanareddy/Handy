# Handy remediation plan

Entry condition: owner approves documentation-only follow-up on SHA `4006c92465b0c90406b4bc2aa6f21c92b3b47806`, with no new motion claims inferred from virtual-arm tests. Exit condition: simulator proof, state recovery, safety boundary, deployment/recovery procedure, UX safety signaling, and independent acceptance are separately documented and verifiable.

## 1) Phased roadmap with entry/exit criteria

### Phase 1 — Baseline/version scope
Exit criteria: one published compatibility matrix covers Node, Python, LeRobot, `serialport`, `websockets`, and `pyserial`; simulator and live-hardware claims use distinct labels.

#### T-01 — Publish the tested runtime and bridge matrix
Linked finding: H-02. Observed paths: `so101-domain-operator/package.json:4-40`, `so101-domain-operator/public/bridge/requirements.txt:1-3`. Proposed path: repo-local compatibility doc or README section.
Prerequisites: review current declarations; identify runtime owners; choose the canonical matrix location.
1. Extract the observed app, bridge, and helper dependencies without inventing missing pins.
2. Publish the exact Node, Python, LeRobot, `serialport`, `websockets`, and `pyserial` versions for the next accepted validation cycle.
3. Mark which entries are hard pins, reviewed bump ranges, or owner-approved changes.
Expected output: authoritative version matrix and update policy.
Verification: a reader can answer “what stack was tested?” without inferring from comments.
- [ ] Node and Python versions are explicit.
- [ ] Bridge/library versions are explicit.
- [ ] Ownership for future version bumps is assigned.

### Phase 2 — Confirmed defect framing and state contract
Exit criteria: simulator proof boundaries are plainly named, and state authority/restore semantics are documented beyond the existing comment.

#### T-02 — Separate simulator proof from hardware proof
Linked findings: H-01, H-03. Observed paths: `so101-domain-operator/vitest.config.ts:4-6`, `so101-domain-operator/src/lib/__tests__/all-missions-live.test.ts:6-11`, `so101-domain-operator/src/lib/__tests__/review.test.ts:128-143`, `so101-domain-operator/src/lib/__tests__/repair.test.ts:117-136,210-216`.
Prerequisites: runtime matrix published; mission owners agree on “dry-run,” “live virtual arm,” and “live hardware”; release notes have a proof section.
1. Reword labels so `npm test`, virtual-arm execution, and supervised hardware acceptance cannot be confused.
2. Keep failed-grasp abort, dry-run non-actuation, and no self-enabled torque as mandatory non-hardware proof.
3. Add a release-proof section stating that virtual-arm success is not bench acceptance.
Expected output: test-scope matrix and release-proof vocabulary.
Verification: misuse of “live” is caught during review.
- [ ] Simulator and hardware claims are separate.
- [ ] Existing negative simulator checks remain required.
- [ ] Release notes reference the correct proof type.

#### T-03 — Define state authority, backup timing, and restore rules
Linked finding: H-04. Observed path: `so101-domain-operator/src/db/index.ts:4-29`. Proposed inspection targets: `so101-domain-operator/src/lib/persist.ts`, `so101-domain-operator/src/app/api/state/route.ts`, schema/migration files if present.
Prerequisites: owner confirms state authority; backup owner is known; one failure scenario is chosen.
1. Turn the current comment into policy: when local state writes, when Postgres writes, and what “backup” excludes.
2. Define outcomes for no database, stale database, and conflicting database cases.
3. Specify what file/log/state snapshot proves successful recovery.
Expected output: written state model and restore decision table.
Verification: reviewers can classify a partial-failure incident without guessing which store wins.
- [ ] Source of truth is named unambiguously.
- [ ] Divergence cases have explicit outcomes.
- [ ] Restore success has observable evidence.

### Phase 3 — Risk-based security and safety boundary
Exit criteria: the team can point to the exact actuation authority boundary and the checks that must pass before motion.

#### T-04 — Trace and constrain the live actuation path
Linked finding: H-03. Observed source anchor: `so101-domain-operator/src/playground/scenarios/index.ts:32-41`. Proposed inspection targets: `so101-domain-operator/src/lib/arm.ts`, `so101-domain-operator/src/lib/missions.ts`, `so101-domain-operator/src/app/api/*`, `so101-domain-operator/public/bridge/so101_bridge.py`.
Prerequisites: proof vocabulary agreed; runtime owners available; team accepts that sample code is not authorized runtime behavior.
1. Trace every path from UI intent or mission selection to the serial write boundary.
2. Mark each hop as advisory, validated, or actuating, and list required checks before torque-on, first motion, and reconnect.
3. Publish a boundary doc separating already-proven simulator invariants from still-unverified live-runtime checks.
Expected output: actuation-boundary map with named validation points.
Verification: an independent reviewer can identify the first line where approved input becomes a serial write.
- [ ] Authority boundary is explicit.
- [ ] Required pre-motion checks are explicit.
- [ ] Disconnect/reconnect handling is explicitly assigned.

### Phase 4 — Meaningful automation
Exit criteria: automation tiers are intentional, negative tests are preserved, and planned commands map to observed scripts.

#### T-05 — Rework the test ladder around proof tiers
Linked findings: H-01, H-03, H-04. Observed paths: `so101-domain-operator/package.json:4-15`, `so101-domain-operator/vitest.config.ts:4-6`, existing tests cited in REVIEW.md sources [S6]-[S11].
Prerequisites: proof vocabulary published; state contract defined; actuation map drafted.
1. Keep `npm test` for deterministic non-hardware checks and document planned use of observed scripts such as `npm run test:live` and `npm run yolo:selftest`.
2. Group tests into unit/protocol, simulated mission, and supervised hardware tiers, with at least one negative test in each.
3. State that test-count growth does not replace boundary coverage, restore drills, or operator-observed acceptance.
Expected output: automation matrix tied to observed scripts and proof tiers.
Verification: reviewers can tell which command belongs to which tier and why lower-tier success is insufficient.
- [ ] Commands come from observed manifest scripts or are clearly marked proposed.
- [ ] Each tier has at least one negative check.
- [ ] Lower-tier success is not presented as release proof.

### Phase 5 — Deployment and recovery operations
Exit criteria: deployment/restart/rollback order is documented for app, bridge, state backup, and serial ownership.

#### T-06 — Write deployment, restart, and restore runbooks
Linked requirement: preventive operations control derived from H-02 and H-04. Observed anchors: `so101-domain-operator/package.json:4-15`, `so101-domain-operator/public/bridge/requirements.txt:1-3`, `so101-domain-operator/src/db/index.ts:4-23`. Proposed paths: ops runbook(s) co-located with the app.
Prerequisites: runtime matrix published; state contract published; owner names deployment target and restart authority.
1. Document startup order for the Next app, Python bridge, optional database, and serial-port ownership checks.
2. Document shutdown/restart order, including torque-safe state before restart and evidence to capture on failure.
3. Define one restore drill for backup divergence or partial-failure restart and record the proving artifacts.
Expected output: deployment/recovery runbook with a named drill.
Verification: an operator can follow the runbook without inventing restart order or restore criteria.
- [ ] Startup order is explicit.
- [ ] Restart/rollback order is explicit.
- [ ] One restore drill has observable pass/fail evidence.

### Phase 6 — UX and safety signaling
Exit criteria: the UI contract names the states users must see before and during motion-capable operation.

#### T-07 — Specify safety-critical UI state signaling
Linked requirement: preventive UX/safety control. Observed anchors: `so101-domain-operator/src/db/index.ts:7-9`, `so101-domain-operator/src/lib/__tests__/all-missions-live.test.ts:7-10`, `so101-domain-operator/src/lib/__tests__/repair.test.ts:117-136,210-216`. Proposed inspection targets: `src/components/*`, state/voice routes, and related UI files.
Prerequisites: actuation map drafted; state contract defined; owner names the human operator role.
1. Define the minimum visible states: sim/live, torque, bridge connected/disconnected, stale backup/divergence, and stop/recovery required.
2. Specify which states block motion, which warn, and which require operator acknowledgment.
3. Add acceptance notes for keyboard reachability and emergency-stop prominence.
Expected output: UI safety-state contract.
Verification: reviewers can judge a screen against the state contract instead of taste.
- [ ] Required visible states are listed.
- [ ] Blocking vs warning states are differentiated.
- [ ] Operator acknowledgment points are explicit.

### Phase 7 — Independent acceptance
Exit criteria: final release evidence comes from an independent reviewer or operator, not only the feature author or simulator output.

#### T-08 — Run independent supervised acceptance
Linked requirement: independent acceptance gate. Observed anchors: current simulator proof in [S6]-[S11]; proposed live evidence path to be defined after T-04/T-07.
Prerequisites: phases 1-6 complete; owner approves live-hardware scope; a non-author reviewer is assigned.
1. Re-run the documented non-hardware gates and confirm their outputs match the proof matrix.
2. Execute the approved supervised hardware checklist, including stop/recovery observation, with the independent reviewer recording results.
3. Produce a signed acceptance artifact that separately records simulator pass, recovery-drill outcome, and live-hardware observations.
Expected output: independent acceptance record with explicit pass/fail sections.
Verification: release reviewers can see who observed what, on which tier, and whether any section stayed unverified.
- [ ] Non-author reviewer/operator is named.
- [ ] Simulator, recovery, and hardware evidence are separate.
- [ ] Acceptance artifact records pass/fail/unknown honestly.

## 2) Task dependency map

| Task | Depends on | Why |
| --- | --- | --- |
| T-01 | none | Establishes the runtime matrix the rest of the plan references. |
| T-02 | T-01 | Proof labels should reference the published compatibility scope. |
| T-03 | T-01 | State policy must reference the approved runtime/storage matrix. |
| T-04 | T-02 | Boundary tracing depends on settled proof vocabulary. |
| T-05 | T-02, T-03, T-04 | Automation tiers need proof names, state semantics, and boundary ownership. |
| T-06 | T-01, T-03 | Deployment/recovery depends on runtime and restore policy. |
| T-07 | T-03, T-04 | UI safety states depend on state semantics and actuation authority. |
| T-08 | T-01 through T-07 | Independent acceptance is last by design. |

## 3) Production acceptance checklist
- [ ] Published compatibility matrix covers app and bridge runtimes.
- [ ] Simulator, virtual-arm, and live-hardware proof claims are distinct.
- [ ] Live actuation boundary and pre-motion checks are documented.
- [ ] State authority, divergence handling, and restore evidence are documented.
- [ ] Deployment, restart, rollback, and one restore drill are documented.
- [ ] UI contract exposes sim/live, torque, disconnect, and recovery states.
- [ ] Independent acceptance artifact records simulator, recovery, and hardware outcomes separately.

## 4) Changes requiring owner approval
- Any new supported runtime or bridge version outside the published matrix.
- Any live-hardware motion, torque-on trial, or serial-port ownership change.
- Any change to the declared source of truth or backup/restore semantics.
- Any deployment target, credential, or database policy change.
- Any release that cites hardware readiness or recovery success.

## 5) Rollback and release policy
Rollback remains software-first and torque-safe: return the arm to the documented safe state before restart or revert, preserve failed-run evidence, and keep divergence/recovery artifacts. Release promotion requires lower-tier evidence plus independent acceptance; simulator success alone is never sufficient. Future command use must come from observed manifest scripts (`npm test`, `npm run test:live`, `npm run yolo:selftest`) or be clearly labeled proposed.
