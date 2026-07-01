# Internal fork — LumiTure Cloud Onboarding (POC)

This repo is the **internal downstream fork** of the public
[`CloudMile-Product/lumiture-cloud-onboard`](https://github.com/CloudMile-Product/lumiture-cloud-onboard).
It mirrors upstream **verbatim** and adds exactly one thing: internal
multi-environment convenience for testing. Everything else (docs, bicep, the public
flow) is upstream's — do not diverge it here.

> The tutorial/README clone URLs point at the public repo (they're upstream's).
> To get the `--env` convenience below, clone **this** fork's URL
> (`CloudBater/lumiture-cloud-shell-poc`), not the URL printed in the docs.

## What this fork adds (the only delta vs upstream)

On `azure/lumiture-azure-onboard.sh` (+ `ENV` passthrough in `azure/onboard-wrapper.sh`):

- **`--env dev|staging|sandbox|prod`** (default `prod`) — resolves the LumiTure
  app-id + billing event-trigger URL for that environment, so internal testing skips
  `--lumiture-app-id` / `--event-trigger-url`. (`ENV=dev bash onboard-wrapper.sh` too.)
- **Auto-detect** the subscription (when exactly one is enabled) + default the export
  storage account/RG — so a bare `--env dev` run needs no other input.

These carry internal env hostnames, which is why they live here and **never** upstream.
If a function app is recreated, refresh the hostnames in the `--env` block.

## Workflow — develop upstream, merge down (never push up)

The public repo is the source of truth and stays clean; this fork only consumes from it.

```bash
# one-time
git remote add upstream https://github.com/CloudMile-Product/lumiture-cloud-onboard.git

# keep this fork current — pulls upstream changes down, no manual copying
git fetch upstream
git merge upstream/main            # or the active upstream branch

# contribute a clean change: make it on the PUBLIC repo (branch + PR there),
# then `git merge upstream/...` down here. NEVER push the --env / auto-detect
# patch upstream — that's what keeps the customer-facing repo clean.
```

Merges stay clean because the only files this fork changes are
`azure/lumiture-azure-onboard.sh` and `azure/onboard-wrapper.sh`; conflicts only
arise if upstream edits the exact lines the `--env` patch touches.
