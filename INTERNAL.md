# Internal fork — LumiTure Cloud Onboarding

`CloudBater/lumiture-cloud-onboard` is the **downstream fork** of
[`CloudMile-Product/lumiture-cloud-onboard`](https://github.com/CloudMile-Product/lumiture-cloud-onboard).
It mirrors upstream closely and adds internal multi-environment convenience for
testing. Everything not listed under the delta below (bicep, the public flow) is
upstream's — do not diverge it here.

> **The two repos share a name and differ only by owner** — `CloudMile-Product` is
> canonical, `CloudBater` is this fork. GitHub shows no "forked from" badge (this repo
> was created standalone, not via fork), so nothing on the page distinguishes them
> except the owner and the banner in `README.md`. Check the owner before you clone,
> and keep that banner intact.

> **This fork is a PUBLIC repo**, despite being internal-facing. The `--env` block
> below therefore publishes the dev/staging/sandbox/prod function hostnames to
> anyone. Those functions are `AuthLevel.ANONYMOUS` with no `?code=`, so the
> hostname is their only obscurity — and it is not obscure. Upstream deliberately
> carries no event-trigger URL; that protection is void as long as this fork is
> public and carries all four. Treat the hostnames as disclosed.

## What this fork adds (the delta vs upstream)

On `azure/init.sh` (+ `ENV` passthrough in `azure/onboard-wrapper.sh`):

- **`--env dev|staging|sandbox|prod`** (default `prod`) — resolves the LumiTure
  app-id + billing event-trigger URL for that environment, so internal testing skips
  `--lumiture-app-id` / `--event-trigger-url`. (`ENV=dev bash onboard-wrapper.sh` too.)
- **Auto-detect** the subscription (when exactly one is enabled) + default the export
  storage account/RG — so a bare `--env dev` run needs no other input.

These carry internal env hostnames and **never** go upstream — that keeps the
customer-facing repo clean regardless of this fork's visibility. If a function app is
recreated, refresh the hostnames in the `--env` block.

On `azure/README.md` + `azure/tutorial.md`: the `git clone` URL points at **this**
fork, not upstream — cloning upstream gets you the script without `--env`.

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

The files this fork changes are `azure/init.sh`, `azure/onboard-wrapper.sh`, and the
clone URL in `azure/README.md` + `azure/tutorial.md`; conflicts only arise if upstream
edits those exact lines. The doc clone URLs are the likeliest to conflict — upstream
owns those files otherwise, so resolve in favour of upstream's prose and keep only the
fork's clone URL.
