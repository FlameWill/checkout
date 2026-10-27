# FlameWill checkout

`actions/checkout`, with a checkout cache on [FlameWill](https://flamewill.com) runners. Everywhere else it is exactly `actions/checkout`.

```yaml
steps:
  - uses: flamewill/checkout@v1   # was: actions/checkout@v4
```

Nothing else to add: no permissions, no secrets. Every `actions/checkout` input works (this release wraps `actions/checkout` v7.0.1).

## How it works

On a FlameWill runner, the repository's full git history waits next to the job. The checkout reads every object it already has from
there and fetches only what is newer, so a large repository checks out in seconds instead of downloading its history again.

- **Every job reads the cache.** Shallow, partial and sparse checkouts all work as they do with `actions/checkout`.
- **Only jobs on the default branch write it.** A push, schedule or manual run on the default branch of the repository itself
  refreshes it at most every few hours, with a full fetch, checked before it is kept. FlameWill decides that from GitHub's own record
  of the run, not from anything in the job, so a pull request or any other branch can never change it.
- **A problem with the cache never fails your job.** The checkout then simply fetches everything, as `actions/checkout` would.

## Inputs

All of `actions/checkout`'s, plus:

| input | default | |
|---|---|---|
| `dissociate` | `false` | Copy the objects the checkout reads from the cache into the repository itself, for later steps that run in a container. |
