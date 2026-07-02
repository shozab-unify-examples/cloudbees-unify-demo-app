# CloudBees Unify Candidate Demo — Application (Release Orchestration)

This is the **Application** repository for the CloudBees Unify SE candidate demo. It holds the **staged** Release Orchestration (RO) workflows that coordinate a release across multiple components, plus a reusable **deployer**.

It is intentionally **separate from the component repository**, because in CloudBees Unify an Application and a Component cannot be the same repo:

- **Component repo** — [`cloudbees-unify-demo-kit`](https://github.com/cb-demos/cloudbees-unify-demo-kit): CI (build/test/scan) and the component's simulated `deploy.yaml`.
- **Application repo** — this repo: staged, governed release workflows + `deployer.yaml`.

**Deployments are simulated** (mock/echo — no real cluster), and **every job publishes evidence** to the run's **Evidence** tab.

## Workflows

- `.cloudbees/workflows/release-finsure-bank.yaml` — **FinSure Bank**, regulated financial services. Staged: governance → **staging** → validation → production approval → **prod** → evidence.
- `.cloudbees/workflows/release-horizon-health.yaml` — **Horizon Health Systems**, healthcare / platform engineering. Staged: golden-path → **qa** → validation → **staging** → summary. (`payments-api` is intentionally not deployed.)
- `.cloudbees/workflows/deployer.yaml` — reusable deployer. For each component in the manifest, it calls that component's simulated `deploy.yaml` (cross-repo). The deploy stages in the release workflows call this deployer per environment.

## Setup required

Because these are **staged** release workflows (real Release Orchestration), a little Unify setup is needed — this is not pure click-and-run:

1. Fork this repo **and** the [component repo](https://github.com/cb-demos/cloudbees-unify-demo-kit), and connect both to your Unify org.
2. Create the **Application** from this repo and add the component to it.
3. Create the target **environments** (`staging`, `prod` for FinSure; `qa`, `staging` for Horizon) and associate them with the Application.
4. Run a release workflow via **Run workflow** and review the **Evidence** tab per job.

> **Cross-repo reference:** `deployer.yaml` calls `cb-demos/cloudbees-unify-demo-kit/.cloudbees/workflows/deploy.yaml@main`. If you forked to a different org, update that path. All three demo components point at the single component repo's simulated deploy; in a real app each would point at its own component repo.

## Supporting props (evidence / talking points)

- `manifests/` — sample release manifests per scenario
- `policies/` — mock governance, approval, and change-control YAML
- `mock-data/` — mock Jira, ServiceNow, security, and release evidence data

## Demo tips

- Run a release end-to-end and walk the Evidence tab job by job to tell the governance/audit story.
- In the Workflow Composer, add a **real manual approval gate** where the `production-approval` step is, to show governance live.
- Pair with the component repo's `ci.yaml` to show the full build → release story across a Component and an Application.
