# ChangeGuard — Hackman X Project Proposal

> **Working title:** ChangeGuard  
> **One-line pitch:** Before you merge a code change, understand what it could break, detect exposed secrets, and verify important risks with real test results.

**Event:** Hackman X, DSCE Bengaluru  
**Dates:** 26–27 October 2026  
**Build constraint:** 24-hour offline hackathon  
**Status:** Candidate project; validate the problem with developers before committing.

---

## 1. Executive summary

ChangeGuard is a developer-tool concept focused on **code-change safety**. It analyzes a proposed code change and produces an evidence-backed report of potential risks before the change is merged.

The long-term vision could bring together five developer-tool workflows:

1. **MockFlow** — generate realistic API mocks.
2. **GitTrace** — analyze the downstream impact of code changes.
3. **TraceLeak** — detect and redact secrets or sensitive information.
4. **PatchLens** — explain risky database migrations.
5. **DocuTest** — generate and execute integration tests.

However, implementing all five as full products in a 24-hour hackathon would be too ambitious. The recommended MVP is narrower: **GitTrace-style impact analysis + TraceLeak-style secret scanning + evidence-backed test execution**.

The product should be useful without pretending to prove that software is completely safe.

## 2. The problem

A small development team can merge changes without a dedicated QA or security engineer. During a code review, developers may need to determine:

- Which functions, modules, or routes depend on changed code?
- Did someone accidentally commit a token, credential, or customer identifier?
- Which existing tests cover the change?
- Could the change introduce a regression?
- Which findings are confirmed by execution, and which are only suspicions?

Existing tools address parts of this workflow. The opportunity is to make the risk and evidence easier to understand in one focused review experience.

**Problem hypothesis:** Developers working in small teams need a fast way to understand the impact of a change and prioritize verification before merging it.

This is a hypothesis, not yet validated demand.

## 3. Target users

Start with a narrow user group:

- Student project teams maintaining shared repositories.
- Solo developers and small teams using AI coding assistants.
- Developers working in TypeScript/Node.js projects without dedicated QA support.

The initial user should be someone who reviews code changes regularly and can test the tool on a real repository.

## 4. Product vision

### Core promise

> See what a code change may affect, identify sensitive data in the diff, and get reproducible evidence for important risks before merging.

### Proposed workflow

1. The developer uploads a Git diff or selects a small supported repository.
2. ChangeGuard parses the changed files and identifies direct dependencies.
3. A secret scanner checks changed lines for common credential patterns and suspicious high-entropy strings.
4. The system runs available tests in an isolated environment.
5. The dashboard presents a prioritized report with code references, evidence, test output, and limitations.
6. The developer decides what to fix and whether to merge.

### Important distinction

Static analysis can identify likely dependencies, but it will not understand every dynamic import, reflection pattern, or runtime relationship. A passing test suite does not prove that a change is bug-free. ChangeGuard must distinguish observed facts from hypotheses.

## 5. The five candidate modules

### A. GitTrace — change impact analysis

**Problem:** A change to a shared utility or type can affect many downstream callers.

**Possible solution:** Parse a supported language, map imports and selected function references, and visualize affected modules.

**MVP:** TypeScript repository, changed-file detection, direct import/call relationships, and a graph highlighting potentially affected callers.

**Limitations:** A static dependency graph is not a complete runtime call graph. Avoid claiming that it can reliably predict every production failure.

### B. TraceLeak — secret scanning

**Problem:** Developers can accidentally commit API keys, tokens, and other sensitive values.

**Possible solution:** Scan changed lines using known credential patterns and entropy heuristics. Redact detected values in reports.

**MVP:** Detect several common secret patterns and high-entropy candidates in a sample diff.

**Limitations:** Detection can produce false positives and false negatives. Do not send secret values to an external model or expose full values in logs.

### C. DocuTest — targeted verification

**Problem:** Developers may not know whether existing tests cover the behavior changed by a patch.

**Possible solution:** Run the repository's existing test suite and optionally generate a targeted test for a selected risk.

**MVP:** Run predefined tests in a safe environment and show actual pass/fail output. Generated tests are an optional stretch goal.

**Limitations:** Tests can be incomplete or incorrect. Test results are evidence about tested behavior, not a proof of total correctness.

### D. PatchLens — migration safety

**Problem:** Database migrations can cause downtime, locks, or compatibility problems.

**Possible solution:** Analyze schema changes and flag known risky migration patterns.

**Recommendation:** Future module. PostgreSQL migration analysis is a distinct workflow and should not be included in the first MVP.

### E. MockFlow — realistic API mocks

**Problem:** Frontend developers may be blocked while backend APIs are unavailable or incomplete.

**Possible solution:** Generate a mock server from an API schema or request and simulate latency, errors, empty responses, and pagination.

**Recommendation:** Future module or separate product. It addresses development-environment setup rather than code-change review.

## 6. Competitor landscape

The basic capabilities in these ideas are not new. Relevant existing products include:

- **GitHub Copilot coding agent:** can work on repository tasks, implement changes, and create pull requests.
- **Sentry Seer:** assists with debugging production issues and proposing fixes.
- **Greptile:** provides AI-assisted code review and repository-aware analysis.
- **Mockoon:** provides API mocking and configurable responses.
- **Gitleaks:** detects secrets in repositories and diffs.
- **GitHub dependency review:** helps identify dependency changes in pull requests.
- **Squawk:** lints PostgreSQL migrations for risky operations.
- **OpenHands and Devin:** provide broader AI software-development workflows.

Official resources:

- GitHub Copilot: https://github.com/features/copilot
- Sentry Seer: https://sentry.io/product/seer/
- Greptile: https://www.greptile.com/
- Mockoon: https://mockoon.com/
- Gitleaks: https://github.com/gitleaks/gitleaks
- GitHub dependency review: https://docs.github.com/en/code-security/supply-chain-security/understanding-your-software-supply-chain/about-dependency-review
- Squawk: https://github.com/sbdchd/squawk
- OpenHands: https://www.openhands.dev/
- Devin: https://devin.ai/

These products demonstrate that simply combining common checks is not a sufficient differentiator.

### Potential differentiation

ChangeGuard should aim to provide a compact, evidence-first review:

- Findings link to the relevant changed code.
- Confirmed failures are separated from speculative risks.
- Test output is real and reproducible.
- Secret values are redacted.
- The interface prioritizes actionable findings rather than generating a large number of comments.

This is a product hypothesis. The team must benchmark it against existing tools rather than claim it is uniquely capable.

## 7. Recommended hackathon MVP

### Build these features

1. **Input:** Upload a Git diff or small TypeScript repository.
2. **Impact analysis:** Parse imports and a limited set of function references.
3. **Secret scanning:** Detect common credential formats and high-entropy candidates.
4. **Verification:** Execute a predefined test suite and display real results.
5. **Dashboard:** Show an impact graph and a concise, prioritized risk report.
6. **Reproducible demo:** Use a small repository with a known regression and a seeded dummy secret.

### Do not build these in the first 24 hours

- Support for every programming language.
- A universal runtime dependency graph.
- A fully autonomous coding agent.
- All five modules in full.
- Automatic merging or guaranteed-safety claims.
- Complex multi-agent orchestration without a clear need.

### Suggested stack

- **Frontend:** React + Vite + TypeScript.
- **Graph visualization:** React Flow.
- **Backend:** Python + FastAPI or Node.js + TypeScript.
- **Parsing:** TypeScript Compiler API for the first supported language.
- **Secret detection:** Pattern-based rules and entropy heuristics.
- **Testing:** Run predefined commands in an isolated environment.
- **Version control:** Git diff and a small fixture repository.

Choose one backend language that the team can implement reliably. Do not add a second language without a clear benefit.

## 8. Architecture

```text
React + Vite dashboard
        |
        v
API / orchestration layer
        |
        +--> Diff parser
        |
        +--> TypeScript dependency analysis
        |
        +--> Secret scanner
        |
        +--> Isolated test runner
        |
        v
Evidence report
  - affected modules
  - secret findings (redacted)
  - test results and logs
  - confirmed failures
  - unverified risks
  - limitations
```

### Security requirements

- Treat uploaded code as untrusted.
- Execute tests in an isolated sandbox or container with resource limits.
- Do not expose host credentials or unrestricted filesystem access.
- Disable network access for test execution unless explicitly needed and controlled.
- Never execute untrusted code directly on the API server.
- Use dummy secrets in the public demo.
- Redact sensitive values from logs and reports.

## 9. The 60-second demo

Use a prepared TypeScript repository with a shared utility, several callers, a seeded dummy credential, and a known regression.

**0–10 seconds — Show the change**

Open a pull request or diff that changes a shared `calculateTotal()` function.

**10–25 seconds — Show the impact**

The dependency graph highlights the affected callers and checkout route.

**25–40 seconds — Show the secret**

The scanner detects the seeded dummy credential and displays a redacted finding.

**40–60 seconds — Show evidence**

Run a targeted test that fails on the changed code. Show the actual test output and explain why the result deserves investigation.

The test must be reproducible. Do not depend on a model inventing a correct answer live.

## 10. Suggested 24-hour schedule

| Elapsed time | Deliverable |
|---|---|
| Hours 0–2 | Agree on scope, fixture repository, API contracts, and acceptance criteria |
| Hours 2–6 | Implement diff parsing and secret scanning |
| Hours 6–10 | Build the dependency analysis and graph data |
| Hours 10–14 | Implement test execution and evidence reporting |
| Hours 14–18 | Integrate the frontend and backend |
| Hours 18–21 | Fix bugs and improve the demo experience |
| Hours 21–23 | Deploy if needed, rehearse, and verify the full workflow |
| Hours 23–24 | Final regression check and submission |

At hour 12, the team should have a working end-to-end path from input to a basic report. If not, reduce scope.

## 11. Suggested four-person team split

- **Member 1 — Dependency analysis:** TypeScript parsing, changed-file detection, graph data.
- **Member 2 — Security and testing:** secret detection, test runner, safe execution.
- **Member 3 — Frontend:** upload flow, graph, findings and report UI.
- **Member 4 — Integration and demo:** API contracts, fixtures, integration tests, deployment and presentation.

For a smaller team, combine roles and remove optional features.

## 12. Validate the problem before committing

Talk to at least five developers who have recently reviewed code changes. Do not start by pitching ChangeGuard.

Ask:

1. Tell me about the last risky code change you reviewed.
2. How did you identify what else might break?
3. Have you ever discovered a secret or regression late in the review process?
4. Which tools do you already use for these tasks?
5. What do those tools fail to catch or make difficult?
6. How much time did the review or investigation take?
7. Would you try a prototype on your next real pull request?

Look for repeated, recent experiences and existing workarounds. Compliments and hypothetical willingness to use a product are weak evidence. Actual use on a real repository is much stronger.

### Discovery sources

- Reddit: https://www.reddit.com/r/webdev/
- Reddit: https://www.reddit.com/r/programming/
- Reddit: https://www.reddit.com/r/SaaS/
- GitHub Issues: https://github.com/issues
- Hacker News Ask: https://news.ycombinator.com/ask
- Indie Hackers: https://www.indiehackers.com/

For PatchPilot/ChangeGuard specifically, GitHub Issues can also provide public bug reports and reproducible test cases.

## 13. How to evaluate the idea

Score each candidate problem from 1–5:

| Criterion | A high score means |
|---|---|
| Pain | The problem is costly or frustrating |
| Frequency | It occurs regularly |
| Existing gap | Current tools leave a meaningful unmet need |
| User access | The team can reach affected developers |
| Feasibility | A credible result can be demonstrated in 24 hours |

Prefer a problem that scores well across all five rather than one with only a spectacular demo.

### Go/no-go criteria

Continue with ChangeGuard if:

- Multiple developers independently describe a similar problem.
- You can identify a meaningful limitation in their existing workflow.
- The MVP works on a real, reproducible example.
- The team can explain the product's value in one sentence.
- The tool produces useful evidence rather than unsupported claims.

Pivot if users do not recognize the problem or existing tools already solve it adequately for the target users.

## 14. Risks and open questions

- **Crowded market:** The component capabilities already exist. Differentiation must be demonstrated.
- **False positives:** Static analysis and secret detection can report harmless cases.
- **False confidence:** Passing tests do not prove complete correctness.
- **Scope creep:** Five products will likely result in five incomplete features.
- **Security:** Running arbitrary repositories requires careful isolation.
- **User demand:** The proposed workflow has not yet been validated with interviews or usage data.
- **Branding:** ChangeGuard is a working title, not a verified available brand or domain.

## 15. Final recommendation

**Build a narrow ChangeGuard MVP for Hackman X, but validate the user problem before committing.**

The strongest initial direction is GitTrace-style impact analysis combined with evidence-backed regression testing. Secret scanning can be a supporting feature if it integrates cleanly. Keep MockFlow and PatchLens out of the first build.

The product should not be pitched as “five tools in one.” Pitch it as:

> **AI makes code changes faster. ChangeGuard helps developers understand their impact and verify important risks before those changes reach production.**

That is a coherent product direction with a visible demo and a plausible path to future expansion. It is not yet proof of market demand or a claim that the product outperforms established competitors.

---

## Immediate next actions

- [ ] Complete Hackman X registration before the deadline.
- [ ] Interview five developers.
- [ ] Compare the proposed workflow with existing tools on the same sample repository.
- [ ] Decide the supported language and exact MVP scope.
- [ ] Prepare a reproducible regression fixture.
- [ ] Define acceptance criteria before implementation.
