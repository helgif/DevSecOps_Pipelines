<!--
This file is generated to help AI coding agents (Copilot-style) become productive
in the `DevSecOps_Pipelines` workspace. The repository contents were empty at
the time of generation, so this document mixes discoverable heuristics with
explicit probing instructions the agent must follow when the user provides
project files.
-->

# Copilot instructions for DevSecOps_Pipelines

Short, actionable points to get productive in this repo. If a referenced file
doesn't exist, ask the user for its path or for a sample of the file.

- Purpose: This repo contains CI/CD and DevSecOps pipeline definitions and
  automation. Typical entry points: `Jenkinsfile`, `Makefile`, `pipeline/`,
  `infra/`, `ci/`, `deploy/`, or YAML files under `.github/workflows`.

- First actions for the agent:
  - Locate pipeline definitions: search for `Jenkinsfile`, `*.yml` under
    `.github/workflows`, `azure-pipelines.yml`, `gitlab-ci.yml`, or `pipeline/`.
  - Locate infra code: search for `terraform/`, `chart/`, `k8s/`, `helm/`.
  - Locate tests: look for `tests/`, `spec/`, or language-specific test files.

- Architecture hints (how to summarise when files exist):
  - Identify primary components (build, lint, test, package, scan, deploy).
  - Map data flows: source → build image/artifact → static analysis/scanning
    → deploy target. Document service boundaries found in `README.md` or
    `docs/` and any `Makefile` targets that orchestrate multiple steps.

- Developer workflows to extract and document:
  - Build commands: parse `Makefile`, `build.sh`, or `package.json` scripts.
  - Test commands: `pytest`, `go test`, `mvn -DskipTests=false test`, etc.
  - Lint/format: any `golangci-lint`, `eslint`, `prettier`, `black` commands.
  - Local debugging: how to run the pipeline locally (e.g., `act`, `docker
    compose -f docker-compose.dev.yml up`) — check for those files.

- Project-specific conventions to surface (examples to look for):
  - Branching for pipelines: check `ci/` or GitHub Actions `if` conditionals
    that reference `refs/heads/main` or `release/*`.
  - Secrets handling: search for `vault`, `sops`, `github.token`, `AZURE_*`.
  - Scanning tools: look for `trivy`, `snyk`, `sonar`, `tfsec`, `gitleaks`.

- Integration points to highlight:
  - External services: cloud provider CLIs, container registries, artifact
    stores (e.g., `gcr.io`, `ghcr.io`, `Azure Container Registry`).
  - Webhooks or GitHub App integration: check `.github/workflows` and
    `README.md` for notices.

- When editing or generating pipeline files:
  - Match existing shell style (bash/zsh) and indentation in YAML.
  - Prefer minimal, revert-safe changes; add comments explaining the reason.
  - Include runnable examples and exact commands to test locally.

- Examples to include in PR or patch messages (when adding automation):
  - "Adds `ci/lint.yml` — runs `make lint` on PRs and pushes linter report as
    a workflow artifact." — include commands to run locally.

- If files are missing (required for deeper analysis), ask the user:
  1. "Please upload or point me to pipeline definitions (Jenkinsfile, GitHub
     Actions YAML, or `pipeline/` directory)."
  2. "Do you have a `Makefile` or `README.md` describing local dev steps?"

- Safety and repo conventions:
  - Never print secrets. If you detect a secret-looking string, warn and ask
    the user whether to rotate it and where secrets are stored.
  - If asked to run commands, provide the exact shell commands and ask for
    permission before executing them locally.

--
If you'd like, I can (1) scan the repository for pipeline files and populate
this document with precise examples, or (2) update an existing
`copilot-instructions.md` if you provide one. Which would you prefer?
