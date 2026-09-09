# Art nightly review: drawn, 2026-09-08

Window: 2026-09-08 00:00 to 2026-09-09 00:00 Europe/Oslo. Frozen main `8dc614c102cc43befbe7ee196fef027497fb62ac`; branch review head `2ebeedc173a301782feb47fcfba2bee525acad49`.

The prior branch repair still rejects Claude Ultra at input, resolved configuration and direct launch boundaries while preserving Codex Ultra, Claude Max, disabled evaluator behavior and persisted selections. No new regression found.

Reviewed exact commits:

- `8dc614c102cc43befbe7ee196fef027497fb62ac`: Full report diff reviewed; historical scope and portable evidence verified against original artifacts. Full-diff SHA-256 `0540b3797cb0368d5ff44940b4e6106d2647b322e12f06c6188e004e59c5ed9d`.
- `2ebeedc173a301782feb47fcfba2bee525acad49`: Full report diff reviewed; historical scope and portable evidence verified against original artifacts. Full-diff SHA-256 `539bb761150ce2f47b8ebc5b6d7fa132c75f0fc9ac7d4348c25d42c672105aaf`.
- `19f899eb909d70d1b153d96f8ad492bf41465d52`: Full report diff reviewed; historical scope and portable evidence verified against original artifacts. Full-diff SHA-256 `0f3dcb41ba61c5b1767879acba050f0c510527f633003f48c370087b9f98ae56`.
- `8266ac421c96b5b7e3fe722b26bbdf6b7a671cb7`: The prior branch repair still rejects Claude Ultra at input, resolved configuration and direct launch boundaries while preserving Codex Ultra, Claude Max, disabled evaluator behavior and persisted selections. No new regression found. Full-diff SHA-256 `040c6d3ba6a343b8cd9941042ade637b089f92b990a6f401ef210a83bb0da222`.
- `38dff87e3faaf73c9045ef391ea6708855206072`: Intentional failing regression test is superseded by the following branch repair; fresh tests pass at the exact reviewed branch head. Full-diff SHA-256 `c8db17ed78d6cf3f8f3ec389420f13640822f2211568f47b3ad6dfc893c26491`.

Required radically-simplify pass covered:

- `apps/desktop/src/shared/generationConfig.ts`
- `apps/desktop/src/main/hiveV2.ts`
- `apps/desktop/src/renderer/App.tsx`

- Retained: schema validation, resolved options and directly exported argument construction have independently callable paths. Removing guards weakens validation.
- Retained: this destroys the visible recovery contract and silently changes user preferences.
- Retained: controls have distinct enablement, preference and quick-pick paths. The small shared pure rule already owns policy; a generic component adds branching and coupling without eliminating independent behavior.

Skill SHA-256 `67e2e706af704089b682ce1197152e384762f1d22968ad06928ac62c5e29eb45`. The second pass found no further compatible, proven reduction. No simplification changes were made.

Fresh checks are 31 core plus 48 desktop tests, typecheck, lint and build. No live model generation or interactive native selector test was run. The existing repair remains on its unmerged feature branch; the main publication is this review only.

Verification excerpts below identify complete local logs by SHA-256. They are portable evidence, with exit status from the command runner.

## drawn-tests

Command: `pnpm test`. Exit `0`. Log SHA-256 `1fe38b30093db8c5d399c12ac278bcf8e09ca196610f931e2bd76128ee259d62`.

```text
packages/core test:  Test Files  3 passed (3)
packages/core test:       Tests  31 passed (31)
packages/core test: Done
apps/desktop test:  Test Files  5 passed (5)
apps/desktop test:       Tests  48 passed (48)
apps/desktop test: Done
```

## drawn-typecheck

Command: `pnpm typecheck`. Exit `0`. Log SHA-256 `70d8b5d4d96f4024529dfb1e9baf07ac9f81a89cfb72072bfc503ace80adb8f5`.

```text
packages/core typecheck: Done
apps/desktop typecheck: Done
```

## drawn-lint

Command: `pnpm lint`. Exit `0`. Log SHA-256 `3146c4be632bc3ede82dd4fd6a325e9e4061ff471f057d5660a72acb2be6ccdf`.

```text

> drawn@0.0.1 lint /Users/trmd/.hive/crew/art/reviews/worktrees/drawn-2026-09-08-root
> eslint .

```

## drawn-build

Command: `pnpm build`. Exit `0`. Log SHA-256 `56c4d2219b6bdaf245c6795f2721cd66e714034b59c448e2c965de1c5c7ec691`.

```text
packages/core build: Done
apps/desktop build: ⚡ Done in 1020ms
apps/desktop build: ⚡ Done in 21ms
apps/desktop build: ✓ built in 3.85s
apps/desktop build: Done
```

This main commit publishes the review only. No unfinished feature branch, shared working-tree changes, deployment or release is included.
