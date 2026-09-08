# Art nightly regression review: Drawn, 2026-09-07

## Scope and disposition

This report records a branch-only review starting from feature head
`24fe66f0d009d7476be0173f33ff42e0ef888d69`. Root reviewed the seven supplied
candidates in full; the authoritative candidate-by-candidate record is
`/Users/trmd/.hive/crew/art/reviews/2026-09-07/drawn-root-reviewed.json`, with
stable patch identities in the adjacent `drawn-patchids.json` and full diffs in
`drawn-diffs.patch`.

| Candidate | Disposition |
| --- | --- |
| `24fe66f0d009d7476be0173f33ff42e0ef888d69` | Reviewed clean: no reproduced packaging, macOS-controls, or evidence regression. |
| `1fdda7a2a499c61e3da20ae3d85dbd0484f1bab4` | Reviewed clean: review documentation only. |
| `b671bef86df0e524ce0a6dea13aa58abcb28a7b4` | Fixed: introduced the Claude Ultra validation defect described below. |
| `05a6d5c509bb89caca6cd7f8b4d501307f793d97` | Reviewed clean: no reproduced packaging regression. |
| `b31cf511486b65addee3bcbc46cb33fc27e89f14` | Fixed through the same correction: patch-equivalent to `b671bef8` (`b8a9d748d3f6acdda4e86321535d96fef39b9152`). |
| `9d831d8da2219dc83d52633ca982a51c811ed917` | Reviewed clean: patch-equivalent to `05a6d5c5` (`6ac25dd0d49e12e7226ae6585f582e22bda265a4`). |
| `75fbfed1fa1f72cf0308eca85345687d43cd2af7` | Fixed through the same correction: patch-equivalent to `b671bef8` (`b8a9d748d3f6acdda4e86321535d96fef39b9152`). |

## Finding: unsupported Claude Ultra effort was accepted

Severity: medium. The tiered-generation patch offered `ultra` for every provider,
accepted persisted or inbound Claude+Ultra options, and passed
`--effort ultra` to Claude. The installed Claude CLI reported the value as
unknown and continued with its default effort, so Drawn could run at a
different effort from the user's selection. The root reproduction is
`drawn-claude-ultra-repro.log`; the failing schema/configuration test is
`drawn-effort-red.log`, and the worker repeated that failure in
`drawn-effort-worker-red.log`.

Commit `8266ac421c96b5b7e3fe722b26bbdf6b7a671cb7` centralizes the provider/effort
rule in `generationConfig.ts` and applies it at the inbound schema, resolved
configuration, and direct Hive-argument boundaries. Codex Ultra and Claude Max
remain supported. An unavailable evaluator effort is ignored when evaluation
is disabled.

The generator and evaluator selectors now disable Claude Ultra. A stale stored
selection or a provider switch that creates the invalid pair is retained and
shown with an actionable status message; generation remains blocked until the
user chooses a valid effort. No model, provider, or preference is reset
silently. Generator quick-pick/provider changes and evaluator provider changes
both retain the effort and therefore take the same visible recovery path.

## Verification

| Command or probe | Outcome | Evidence |
| --- | --- | --- |
| Focused generation-config and Hive adapter tests before the fix | Failed as expected: Claude Ultra was admitted | `/Users/trmd/.hive/crew/art/reviews/2026-09-07/drawn-effort-worker-red.log` |
| `pnpm --filter @drawn/desktop test -- generationConfig.test.ts hiveV2.test.ts` | Passed, 24/24 | `/Users/trmd/.hive/crew/art/reviews/2026-09-07/drawn-effort-focused-green.log` |
| `pnpm typecheck` | Passed | `/Users/trmd/.hive/crew/art/reviews/2026-09-07/drawn-typecheck.log` |
| `pnpm test` | Passed, 79/79 | `/Users/trmd/.hive/crew/art/reviews/2026-09-07/drawn-test.log` |
| `pnpm lint` | Passed | `/Users/trmd/.hive/crew/art/reviews/2026-09-07/drawn-lint.log` |
| `pnpm build` | Passed | `/Users/trmd/.hive/crew/art/reviews/2026-09-07/drawn-build.log` |
| Read-only bundled adapter against live Hive | Connection and provider catalog passed | `/Users/trmd/.hive/crew/art/reviews/2026-09-07/drawn-live-hive-read.log` |

The selector paths were inspected in source and compiled in the production
renderer build. No model agents were launched. A deterministic renderer fixture
was not available in this branch, so this report does not claim interactive UI
verification.

## Boundaries

The correction is local to the unmerged Drawn feature branch. Nothing was
pushed, installed, or deployed. Compatibility beyond the proven Claude Ultra
pair was intentionally left unchanged.

## Main publication scope

Frozen remote main was `72fec0b4831dc2a287ecc48c148f93c8e99b1018`. All seven daily candidates are outside that main history. This main commit contains only the review report. The local repair remains on `art/nightly-2026-09-07-drawn-local`; its test commit is `38dff87e3faaf73c9045ef391ea6708855206072` and its fix is `8266ac421c96b5b7e3fe722b26bbdf6b7a671cb7`. Parent reviewed the complete fix and schema, saved-preference, selector and launch call sites. The first nightly review does not claim coverage of older history before the requested day.
