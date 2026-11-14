---
inclusion: auto
name: codifide-governance-gates
description: Full criteria for Harness gates G0, G1, G2/G3, G4, G5, G6, the G-LOOP, the tripwires that set process depth (Express/Standard/Enterprise), and governance principles. Load when running, preparing, or assessing any gate, or when selecting a track.
# For non-Kiro tools, load this file on demand when gate work begins.
---

# Governance Gates

Every initiative passes through **six gates**: G0, G1, G2/G3 (one combined design-and-architecture gate), G4, G5, G6. No gate passes on confidence, only on evidence. Between gates, automated loops (G-LOOP) verify consistency, run tests, and catch drift.

**Authority:** the Human Steward decides every gate (see `00-welcome.md` §1). AI personas produce evidence and recommendations. Aegis may block a gate for missing evidence; it may never pass one.

**Review requirements** for each gate and track are defined once, in `00-welcome.md` §3. This file does not restate them.

**Intent Contract:** required at G0 for Standard and Enterprise (`templates/INTENT-CONTRACT.md`). Express uses an inline Intent block in the combined G0/G1 note. Which depth applies is computed from what the work touches ("Tripwires and depth" below), not chosen up front.

## The Gates

| Gate | Question | Evidence owners (AI personas) | Decision |
|------|----------|-------------------------------|----------|
| **G0** | Is this worth exploring? | Harper, Mary, Primum | Human Steward |
| **G1** | Are the requirements and evidence strong enough? | Harper, Mary, Forge, Ruth | Human Steward |
| **G2/G3** | Is the design/architecture ready? | Winston, Sentinel, Tessa | Human Steward (+ Architecture/Security Lead on Enterprise) |
| **G4** | Build and verify. | Amelia, Tessa, Sentinel, Primum | Human Steward |
| **G5** | Release readiness. | Sable, Sentinel, Ruth, Forge | Human Steward |
| **G6** | Post-release review. | Sable, Harper, Quill | Human Steward |

---

## G-LOOP — Automated Verification Between Gates

Between gates, automated loops run continuously. They don't require human approval but DO log their results.

**G-LOOP handles:**
- Running test suites after every change
- Verifying data consistency (row counts, checksums, view freshness)
- Build verification (compile, lint, type-check)
- Regression detection (benchmarks, API contract compliance)
- Drift monitoring (schema, dependency, config divergence)
- Intent-integrity checks (scope drift, protected-constraint regression, verifier manipulation)
- Computing the Review Level floor for each change (`11`)

**G-LOOP does NOT handle:**
- Design decisions or architecture changes
- Compliance interpretation
- Anything requiring "it depends" reasoning
- Redefining intent, waiving harm boundaries, or accepting residual risk
- Passing a gate

**Rule:** If a verifier can judge it, it belongs in G-LOOP. If it needs human judgment, it goes through a gate.

---

## G0 — Is This Worth Exploring?

**Must include:**
- Business problem statement
- Target users and workflow
- Risk classification (sensitive data, identity, release logic?)
- Stakeholders identified
- Scope boundaries (in / out)
- Proposed track with rationale
- Primum intent check (fresh context, `00` §5) against the draft Intent Contract
- Intent Contract (Standard/Enterprise) or inline Intent block (Express), naming the Human Steward

**Passes when:** The Human Steward agrees the problem is worth solving, the scope is honest, and the track is right.

**Template:** `specifications/{ID}/G0_PROBLEM_STATEMENT.md`

### Loop-Eligible Tasks at G0
- Stakeholder identification from org charts and prior specs
- Risk classification lookup against known patterns
- Duplicate detection (has this been proposed before?)

---

## G1 — Are the Requirements and Evidence Strong Enough?

**Must include:**
- Evidence-backed requirements with acceptance criteria
- Glossary and business rules
- NFRs with measurable targets, KPIs with baselines, and Benchmarks (`05`)
- API contracts or integration specifications
- Backend traceability (if integrating with existing systems)
- Open assumptions documented
- Gate review completed at the level required by `00` §3, findings dispositioned
- Intent Contract traced into acceptance criteria and NFRs
- Primum intent check (fresh context) passed

**Passes when:** Requirements are testable, traceable, and survived adversarial review.

**Template:** `specifications/{ID}/G1_REQUIREMENTS.md`

### Loop-Eligible Tasks at G1
- Acceptance-criteria completeness check
- Glossary consistency verification
- NFR target validation (measurable, not subjective)
- API contract schema validation
- Traceability matrix generation (requirement → test)

---

## G2/G3 — Is the Design/Architecture Ready?

G2/G3 is **one gate**. The label is historical (design and architecture used to be separate gates); there is no standalone G2 or G3.

**Must include:**
- Architecture overview with module boundaries
- ADRs for significant decisions (with alternatives considered), including any new cloud service or account (requires Human Steward approval)
- Threat model (attack surfaces, mitigations, residual risk)
- Data flow and trust boundaries
- Test strategy
- Decomposed tickets with acceptance criteria
- 3 Amigos approval on each execution ticket (the product, development, and test owners for the ticket; humans where the organization has them, otherwise the Human Steward)
- Gate review at the level required by `00` §3
- Authority boundaries and irreversible-action controls for agents/loops, including `.codifide/immutable-paths`

**Passes when:** The design is implementable, secure, and testable, and the team knows what to build.

**Template:** `specifications/{ID}/G2_ARCHITECTURE.md` + `ADR-*.md` + `THREAT-MODEL.md`

### Loop-Eligible Tasks at G2/G3
- Dependency vulnerability scanning on the proposed stack
- Interface contract validation across modules
- Architecture drift detection against ADRs
- Ticket decomposition verification

---

## G4 — Build and Verify

**Must include:**
- Code complete against ticket acceptance criteria
- Coverage meets the targets in `03-coding-standards.md` (the single coverage table). Exceptions are approved by the Human Steward.
- Security review passed (no open CRITICALs)
- Accessibility audit passed
- Every change reviewed at its Review Level (`11`), with findings dispositioned
- Traceability updated (requirement → test → verification)
- Primum intent-integrity check passed with no unresolved HALT
- Any verifier, baseline, threshold, or immutable-path change approved by the Human Steward as its own change

**Passes when:** The code works, is proven to work, and the proof is documented.

**Template:** `specifications/{ID}/G4_VERIFICATION.md`

### Loop-Eligible Tasks at G4
- Continuous test execution on every commit
- Coverage enforcement
- Security scan automation (SAST, SCA, secret detection)
- Accessibility linting
- Build verification
- Performance regression detection against an approved baseline
- Immutable-path check on every PR (`templates/enforcement/check-immutable-paths.sh`)

---

## G5 — Release Readiness

**Must include:**
- Release notes
- Rollback plan (tested, not theoretical)
- Monitoring and alerting configured
- Penetration test completed, **or** residual risk accepted in writing by the Human Steward with rationale
- Compliance review completed (if applicable), signed by the Compliance Officer role
- NFR targets met (with evidence)
- KPI baseline collected (or measurement plan documented)
- Current AI-BOM (`10`) if AI components are used
- Gate review at the level required by `00` §3, verdict PASS, or PASS WITH CONDITIONS where every condition has an owner and a deadline and the Human Steward has accepted the conditions in writing (`04`)
- Intent Contract revalidated against release behavior and protected constraints

**Passes when:** The Human Steward is satisfied the system is safe to put in front of users.

**Template:** `specifications/{ID}/G5_RELEASE_READINESS.md`

### Loop-Eligible Tasks at G5
- Rollback dry-run
- Monitoring configuration validation
- Alert threshold testing
- NFR evidence collection
- Release artifact integrity (checksums, signatures)

---

## G6 — Post-Release Review

**Must include:**
- Production observations (7-day minimum)
- KPI actuals vs. targets
- Incident review (if any)
- Reliability metrics
- User feedback summary
- Follow-up actions identified
- Quill lifecycle narrative completed (`specifications/{ID}/QUILL_LOG.md`)

**Passes when:** We've learned from the release and captured it for the next one.

**Template:** `specifications/{ID}/G6_RETROSPECTIVE.md`

### Loop-Eligible Tasks at G6
- KPI collection and dashboard updates
- Error-rate monitoring and trend detection
- SLA compliance reporting
- Loop health monitoring

---

## Principles

1. **User safety outranks throughput.** We don't ship faster by skipping gates.
2. **Evidence, not confidence.** "We think it's fine" is not a gate pass.
3. **Independent adversarial review, sized to risk.** Review levels per `00` §3. Never self-conducted.
4. **No single persona self-approves, and no AI approves.** AI recommends; humans decide.
5. **NFRs, KPIs, and Benchmarks are mandatory** for Standard and Enterprise; Express states NFR impact (or "no NFR change") in its G0/G1 note.
6. **Security and accessibility are every-phase.** Not a late fix.
7. **Automate repeatable assurance.** Coverage is evidence, not proof of correctness.
8. **Kill early, kill cheap.** The answer to a bad idea is G0 rejection, not G5 failure.
9. **Loop the mechanical, gate the judgment.**
10. **Intent is a control surface.** Agents may optimize execution, not redefine the goal.
11. **Do no harm.** When execution conflicts with intent or harm boundaries, autonomy yields to HALT.

---

## Tripwires and depth

**Nobody picks a mode.** A choice made before the work starts gets skipped, and at the start nobody knows what the work will turn into. So work starts light, with no declaration, and process is added only when the work trips a wire. Heavy process on work that cannot hurt anyone is waste; light process on work that can is negligence. Tripwires put the weight where the risk is, at the moment it appears.

"Express", "Standard" and "Enterprise" are the three **depths** of process. Earlier versions called them tracks and had the Human Steward select one at G0. They are now the result of the tripwires, confirmed by the Human Steward.

### Before any tripwire: just work

Exploring, prototyping, trying an approach on synthetic data, on a branch nobody depends on: no gate, no gate review, no note. Say in a line what you are trying. Failing fast is a result; stopping needs no ceremony.

Three things hold even here: the security non-negotiables (`00` §6), verifier immutability, and HALT.

### The tripwires

| # | Tripwire | Who detects it | What happens |
|---|---|---|---|
| 1 | Real sensitive data, a real credential, a production system, spending money or creating cloud resources, or anything irreversible | The agent (a rule, not a script) | Stop and ask the Human Steward before doing it (`00` §6). If the work will handle sensitive data from here on, the depth is Enterprise from this moment |
| 2 | A write to a verifier, baseline, or anything in `.codifide/immutable-paths` | The write hook, and the CI check | Blocked. It is a separate, human-approved change |
| 3 | The work is proposed for the protected branch | `check-review-level.sh` in CI | It computes the Review Level from the changed paths and the size of the change, and prints what is owed before merge |
| 4 | The change touches a sensitive path: the conventional ones (auth, crypto, keys, migrations, production infrastructure) or any path the repository lists in `.codifide/sensitive-paths` | The same CI check | Highest Review Level (R3); Enterprise depth |
| 5 | The change is large (over 500 lines across more than 5 files) | The same CI check | R3 |
| 6 | Someone else will depend on the output, or it is to be released | The agent and the Human Steward | The release gates apply (G5, and G6 after) |

`.codifide/sensitive-paths` is how a repository sets its own tripwires: a short, human-owned list of the places where a mistake hurts a person, loses money, or cannot be undone. It is read from the base branch, so the change being judged cannot shorten it. Example: `templates/enforcement/sensitive-paths.example`.

### From tripwire to depth

When tripwire 3 fires, the agent proposes a depth with its reason, in one sentence, and the Human Steward confirms or changes it. The agent proposes; it does not ask the human to classify the work from a table.

| What the tripwires found | Depth | Gate path | Evidence owed |
|---|---|---|---|
| Only tests, docs or styles changed (R1) | none | Merge on green checks | The automated checks |
| A small, additive change on an existing architecture; no sensitive path; not large | **Express** | One short G0/G1 note → G4 → human ship decision | Inline Intent block; NFR impact statement; change reviews at their Review Level; G-LOOP; rollback note |
| A new feature, new architecture, or a large change | **Standard** | G0 → G1 → G2/G3 → G4 → G5 → G6 | Full gate evidence; Intent Contract |
| Tripwire 1 or 4: sensitive data, identity, release logic, regulated, irreversible, high blast radius | **Enterprise** | The same six gates | Full gate evidence; Intent Contract; domain-expert sign-off (human SME); review levels per `00` §3 |

- **Depth follows what the work touches now, not what the product may touch later.** A synthetic-data prototype of a product that will one day hold sensitive data has tripped nothing. It reaches Enterprise at the first real record, credential, or release.
- **Exploration buys no shortcut.** Work done before a tripwire has had no review. When it is first proposed for the protected branch it is reviewed as new code, at the level its paths require, and the gates for its depth are run then. The speed comes from deferring process until it is warranted, never from skipping it for what ships.
- **Depth only goes up by itself.** Lowering it is the Human Steward's decision. If Express work turns out to touch a security surface, it stops and re-enters at the higher depth.
- **A human can always raise it.** Path rules are crude: they miss risk that does not show in a file name. The Human Steward, or the agent, can declare a higher depth at any time, with the reason.

### Express at a glance

For a small, safe change to something that already exists.

- **You write** one note, usually under a page: the problem and scope; the Intent block (goal, scope, protected constraints, harm boundary, rollback); the NFR impact, or "no NFR change" and why.
- **Reviews:** one focused review (R2) of that note and Primum's intent check; each code change gets the Review Level its paths require (`11`).
- **Not needed:** separate G0 and G1; G2/G3 (the architecture already exists); G5 as a gate (it is a human ship decision with a rollback note); G6; the full Intent Contract; the full NFR document.
- **Example.** A read-only CSV export of an existing report, in a service already in production: additive, no new data, no new permission. If it turns out to need a new permission or to include a sensitive field, that is tripwire 4: stop and re-enter higher.

### What is enforced and what is not

- **Enforced by script** (when the CI check is a required status check): the Review Level floor from paths, declared sensitive paths, the size rule, and immutable paths.
- **Not enforced by any script:** tripwires 1 and 6, the choice between Express and Standard, and whether the reviews a level requires were actually run. `Review-Level` in a commit is a declaration. These rest on the agent following this file and on the Human Steward.
- **Nothing is checked until work is proposed for the protected branch.** That is the design: it is what makes exploration free. It also means a repository with no required status check has no tripwire 3, 4 or 5.

**What never changes:** G-LOOP runs; evidence over confidence; the Human Steward makes the go/kill/hold decision.

---

## For Existing Projects

See `06-existing-project-assessment.md`. In short: assess each gate, close security gaps first (a missing threat model for a system handling user data is always P0), apply gates to new work going forward, and establish G-LOOP immediately.
