# pip_constraints-txt-direct-dep

**What this probe proves:** `constraints.txt` enforcement also applies to **direct** deps, not just transitives.

## Real-world scenario

A team declares `requests` as a direct dep in `requirements.txt` without a version specifier. Their security baseline pins it to `2.31.0`. Dropping that pin into `constraints.txt` should resolve `requests` to `2.31.0` at scan time.

## Files

- `requirements.txt` — `requests` (unpinned, intentional)
- `constraints.txt` — `requests==2.31.0`
- `whitesource.config` — `applyConstraints=true`

## Expected scan behavior (post SCA-5154)

Mend's dep tree reports:
- Direct: `requests` at **`2.31.0`** (NOT the latest version pip otherwise resolves)
- Transitives consistent with `requests` 2.31.0's release-time deps.

## Pairing with other probes

| Probe | constraint target | proves |
|---|---|---|
| `pip_constraints-txt-basic` | transitive (urllib3) | basic auto-discovery + transitive pinning |
| `pip_constraints-txt-direct-dep` (this one) | **direct** (requests) | direct deps are also constrained |
| `pip_constraints-txt-range-exclude` | transitive range | range operator (SCA-5692) |
| `pip_constraints-txt-multi-pin` | two transitives | multiple entries in one file |
| `pip_constraints-txt-flag-off` | (none — flag off) | feature flag gates behavior |