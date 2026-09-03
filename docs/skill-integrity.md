# Skill capability integrity

The `vibe` skill teaches an agent to move real money: swaps, perps, wallet
operations, token launches. Its whole chain of trust starts with one line that
installs the CLI straight from the network:

```bash
curl -fsSL https://api.vibe.airforce/api/cli/install.sh | sh
```

Nothing else in this repo notices if that line — or any other reach in the
skill — changes **after** a review. A pull request that swaps the install host,
or adds a second call to a new endpoint, reads like an ordinary edit. That
post-admission change is the classic supply-chain **rug pull**, and
`team-auto-approve.yml` approves team PRs on sight, so a status check is the
real guard.

[`ci-skill-integrity`](../.github/workflows/ci-skill-integrity.yml) closes that
gap with [eyebrow](https://github.com/alexverify/eyebrow), a content-addressed
integrity engine — and does it **without** firing on every wording change.

## What it gates on

[`eyebrowlock.json`](../eyebrowlock.json) fingerprints every
`skills/<slug>/SKILL.md`: its content hash **and** its declared egress — the set
of hosts the skill reaches from a network-call line (`curl`, `wget`,
`WebFetch`). On every PR that touches a skill, the workflow re-derives that
fingerprint and, per [`eyebrow.policy.json`](../eyebrow.policy.json), fails the
build **only** when a skill:

- **gains a new egress host** vs the lockfile (`failOnCapabilityExpansion`), or
- introduces a **new critical finding** (`failOnSeverity: critical`).

It **does not** fail on wording changes (`allowContentDrift: true`). Prose edits
that keep the same hosts are reported but pass. This catalog reworks its
85-command guidance constantly; a gate that fired on every byte would be
switched off within a week — the exact failure mode of an over-broad scanner.
eyebrow gates on *reach*, not wording, so the signal stays meaningful.

## The install line is a known baseline

eyebrow reads `curl ... | sh` as a **critical** pipe-to-shell finding: the host
can serve any script it likes, and the skill runs it unread. That is a real
risk, not a false positive. The lockfile records it as the **accepted
baseline**, so the gate does not block on it today — it blocks only on reach or
critical findings that appear *later*.

To retire the baseline, pin the installer by checksum (fetch, verify a known
hash, then run) instead of piping the network into a shell. Once the skill no
longer carries a critical finding, the gate has less to grandfather.

**Scope, honestly:** the egress parser is line-based and host-granular. It
catches the common rug-pull shape — a new endpoint on a call line. It does
**not** catch a URL split across lines, one assembled from shell variables, or a
new *path* on an already-listed host. Treat this gate as defense-in-depth, not a
bash parser.

## Refreshing the lockfile

When a skill legitimately gains a new endpoint, regenerate the fingerprint in
the **same PR**:

```bash
# one-time: install eyebrow — see github.com/alexverify/eyebrow/releases
eyebrow scan --path . --lockfile eyebrowlock.json
git add eyebrowlock.json
```

The diff on `eyebrowlock.json` shows exactly which skill's reach changed,
reviewed alongside the skill change itself. A skill that adds a new egress host
with no matching lockfile update is what the gate rejects.

## Notes

- `skills-lock.json` at the repo root marks this as a managed skills catalog;
  eyebrow keys its discovery on that marker and stays inert on any repo without
  it.
- `CLAUDE.md` is fingerprinted too — it carries agent instructions, so its
  integrity matters for the same reason a skill's does.
- The action is pinned by commit SHA; the `version` input selects the eyebrow
  release, and its published checksums guard the download in transit.
