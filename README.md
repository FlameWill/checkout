# FlameWill checkout

`actions/checkout`, with a checkout cache on [FlameWill](https://flamewill.com) runners. Everywhere else it is exactly `actions/checkout`.

```yaml
permissions:
  contents: read
  id-token: write   # lets jobs on the default branch refresh the cache

steps:
  - uses: flamewill/checkout@v1   # was: actions/checkout@v4
```

Every `actions/checkout` input works unchanged (this release wraps `actions/checkout` v7.0.1).

## How it works

On a FlameWill runner, the repository's full git history waits next to the job. The checkout reads every object it already has from
there and fetches only what is newer, so a large repository checks out in seconds instead of downloading its history again.

- **Every job reads the cache.** Shallow, partial and sparse checkouts all work as they do with `actions/checkout`.
- **Only jobs on the default branch write it.** A push, schedule or manual run on the default branch refreshes it at most every few
  hours, with a full fetch, checked before it is kept. A pull request or any other branch can never change it. Writing needs
  `permissions: id-token: write`; without it the cache is still read.
- **A problem with the cache never fails your job.** The checkout then simply fetches everything, as `actions/checkout` would.

## Inputs

All of `actions/checkout`'s, plus:

| input | default | |
|---|---|---|
| `dissociate` | `false` | Copy the objects the checkout reads from the cache into the repository itself, for later steps that run in a container. |
