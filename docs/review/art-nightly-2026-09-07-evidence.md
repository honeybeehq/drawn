# Art nightly drawn evidence, 2026-09-07

These excerpts and SHA-256 digests make the local verification record readable in the repository. Each digest identifies the original complete log. Excerpts omit routine progress output. Exit codes are recorded by the command runner; an empty lint log alone is not treated as proof.

Source and fix revisions below refer to the reviewed feature branches where indicated. Publishing this document does not publish those feature branches or claim a live UI/model run.

- Claude Ultra effort passed argument validation. Source `b671bef86df0e524ce0a6dea13aa58abcb28a7b4`; repair `8266ac421c96b5b7e3fe722b26bbdf6b7a671cb7`. fixed on local branch.

## drawn-effort-focused-green.log

Command: `pnpm --filter @drawn/desktop test -- generationConfig.test.ts hiveV2.test.ts`

Recorded exit: `0`. Original bytes: `261`. SHA-256: `a15396046be3af457221a2d9c4fd5b355d87e5f9b9ee0eab06ca6b5d974e27e8`.

```text
 RUN  v4.1.11 /Users/trmd/.hive/crew/art/reviews/worktrees/drawn-2026-09-07/apps/desktop

 Test Files  2 passed (2)
      Tests  24 passed (24)
   Start at  05:08:04
   Duration  6.97s (transform 215ms, setup 0ms, import 657ms, tests 6.26s, environment 2ms)

```

## drawn-typecheck.log

Command: `pnpm typecheck`

Recorded exit: `0`. Original bytes: `309`. SHA-256: `82bb7833d084dfe318d075e9e586f7483fb7fa6a3f625eeeb8dd07a9e40e8fd7`.

```text
> drawn@0.0.1 typecheck /Users/trmd/.hive/crew/art/reviews/worktrees/drawn-2026-09-07
> pnpm -r typecheck
Scope: 2 of 3 workspace projects
packages/core typecheck$ tsc -p tsconfig.json --noEmit
packages/core typecheck: Done
apps/desktop typecheck$ tsc -p tsconfig.json --noEmit
apps/desktop typecheck: Done
```

## drawn-test.log

Command: `pnpm test`

Recorded exit: `0`. Original bytes: `1003`. SHA-256: `5d5cd7c344b163353e78d16af02c3b60cec918c7162b1d7d40cdac1e54e50826`.

```text
packages/core test:  RUN  v4.1.11 /Users/trmd/.hive/crew/art/reviews/worktrees/drawn-2026-09-07/packages/core
packages/core test:  Test Files  3 passed (3)
packages/core test:       Tests  31 passed (31)
packages/core test:    Start at  05:08:56
packages/core test:    Duration  2.11s (transform 1.10s, setup 0ms, import 2.80s, tests 215ms, environment 0ms)
packages/core test: Done
apps/desktop test$ vitest run --config vitest.config.ts
apps/desktop test:  RUN  v4.1.11 /Users/trmd/.hive/crew/art/reviews/worktrees/drawn-2026-09-07/apps/desktop
apps/desktop test:  Test Files  5 passed (5)
apps/desktop test:       Tests  48 passed (48)
apps/desktop test:    Start at  05:09:00
apps/desktop test:    Duration  8.24s (transform 1.86s, setup 0ms, import 4.51s, tests 9.09s, environment 1ms)
apps/desktop test: Done
```

## drawn-lint.log

Command: `pnpm lint`

Recorded exit: `0`. Original bytes: `94`. SHA-256: `656e08fedfc732a5cdb065cb89259d483e561a6aa7031bcae3df96f0db753980`.

```text
> drawn@0.0.1 lint /Users/trmd/.hive/crew/art/reviews/worktrees/drawn-2026-09-07
> eslint .
```

## drawn-build.log

Command: `pnpm build`

Recorded exit: `0`. Original bytes: `7372`. SHA-256: `0869921f7c1fefa375032f7121422a3e70ffde9ce876e65ea38c9b593cbec512`.

```text
apps/desktop build: dist/renderer/assets/inter-latin-ext-400-normal-77YHD8bZ.woff               47.56 kB
apps/desktop build: dist/renderer/assets/inter-latin-ext-500-normal-BxGbmqWO.woff               48.49 kB
apps/desktop build: dist/renderer/assets/inter-latin-ext-600-normal-CIVaiw4L.woff               48.66 kB
apps/desktop build: dist/renderer/assets/index-D87puB9C.css                                     38.69 kB │ gzip:   7.21 kB
apps/desktop build: dist/renderer/assets/index-BSHL2G8w.js                                     385.12 kB │ gzip: 112.84 kB
apps/desktop build: ✓ built in 1.02s
apps/desktop build: Done
```

## drawn-effort-worker-red.log

Command: `Focused regression reproduction before the repair`

Recorded exit: `expected failing test`. Original bytes: `1627`. SHA-256: `b0f5ac9255993a91c6bfc5df06f8bc0a8f5af4b199d9fe8629e7dd86320fdf1f`.

```text

 ❯ test/generationConfig.test.ts (7 tests | 1 failed) 48ms
     × rejects Claude ultra effort before admitting a generation request 30ms

⎯⎯⎯⎯⎯⎯⎯ Failed Tests 1 ⎯⎯⎯⎯⎯⎯⎯

 FAIL  test/generationConfig.test.ts > generation configuration > rejects Claude ultra effort before admitting a generation request
AssertionError: expected true to be false // Object.is equality


 Test Files  1 failed | 4 passed (5)
      Tests  1 failed | 46 passed (47)
   Start at  05:06:37
   Duration  7.57s (transform 1.43s, setup 0ms, import 3.69s, tests 8.27s, environment 4ms)

/Users/trmd/.hive/crew/art/reviews/worktrees/drawn-2026-09-07/apps/desktop:
 ERR_PNPM_RECURSIVE_RUN_FIRST_FAIL  @drawn/desktop@0.0.1 test: `vitest run --config vitest.config.ts -- generationConfig.test.ts`
Exit status 1
```
