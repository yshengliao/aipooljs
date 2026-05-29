# Changelog

All notable changes to this project are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project
adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.3.0] - 2026-05-29

### Added

- `onOverflow` option (`'throw' | 'null' | 'grow' | (pool) => T`) on `PoolOptions<T>`. Default
  remains `'throw'` — fully backward-compatible.
- `NullPool<T>` interface and overloaded `createPool` factory: `onOverflow: 'null'` narrows
  `acquire()` return type to `T | null` at compile time.
- `OverflowHandler<T>` type exported.
- `borrow(fn, opts?)` helper on `Pool<T>`: auto-releases via `try/finally`, with opt-in
  `AbortSignal` cancellation. Sync and async `fn` both supported. Six stability invariants
  documented in `STABILITY.md`.
- `STABILITY.md` — stable API surface + borrow invariants + polymorphic-chunked-pool draft
  placeholder.

### Changed

- Size budget `dist/index.js`: 700 B → 850 B (accounts for `onOverflow` dispatch + `borrow`
  async/abort machinery).

### Docs

- README roadmap: `borrow` + `onOverflow` moved from the never-shipped 0.2.0 row to **0.3.0**.
- README status line: "0.1.0 published" → "0.3.0 published".
- README Capabilities table: "Auto-grow (overflow throws PoolError)" updated to reflect opt-in
  `onOverflow` (`'grow'` available; default still throws).
- `STABILITY.md` added to `llms-full.txt` via `scripts/build-llms-full.mjs`.

## [0.1.1] - 2026-05-28

### Changed (CI)

- **`publish.yml` now triggers on `push: tags: ["v*"]`** (was `workflow_dispatch` only). Aligns with the trigger used by `aifsmjs` / `aiecsjs` / `aibridgejs`. Tag push now automatically runs the OIDC trusted publish.
- **`npm publish --provenance --access public`** — the workflow now emits a [sigstore provenance attestation](https://docs.npmjs.com/generating-provenance-statements) so consumers can verify the tarball was built by this workflow on this commit.

No runtime / source / API changes. This is a CI-only patch to validate the GitHub Actions OIDC trusted-publisher pipeline now that the npm trusted publisher entry is configured. Production bundles are byte-identical to 0.1.0.

## [0.1.0] - 2026-05-28

### Added

- `createPool({ create, reset, size })` factory — fixed-size, fail-fast on overflow.
- `acquire()` / `release()` / `drain()` / `dispose()` lifecycle.
- Double-release detection via `Set`-tracked alive set; offending `release()` throws `PoolError`.
- `dispose()` is idempotent; subsequent `acquire()` / `release()` / `drain()` throw `PoolDisposedError`.
- `alive` / `available` / `disposed` read-only counters.
- Test coverage ≥95% statements / lines / functions / ≥90% branches.
- Size budget: ≤500 B gzip (raised to 700 B if strict-TS overhead pushes past 500 B).
- Dual ESM + CJS build via `tsup` with `minify: true`; `sideEffects: false`; zero runtime dependencies.

## [0.0.1] - 2026-05-28

### Added (scaffold)

- Full package scaffold landed (`package.json`, `tsconfig.json`,
  `tsconfig.test.json`, `tsup.config.ts`, `vitest.config.ts`, `biome.json`,
  `scripts/{verify-exports,check-size,build-llms-full}.mjs`,
  `test/scaffold.test.ts`, `examples/.gitkeep`, `.github/workflows/{ci,publish}.yml`,
  `llms.txt`, `llms-full.txt`).
- `src/index.ts` remains a `throw` stub exposing the frozen 0.1.0 API surface
  (`createPool`, `Pool<T>`, `PoolOptions<T>`, `PoolError`, `PoolDisposedError`).
- `pnpm typecheck && pnpm lint && pnpm coverage && pnpm build &&
  pnpm verify:exports && pnpm verify:llms && pnpm check:size` walks clean
  against a single placeholder test.
- Coverage thresholds temporarily set to `0/0/0/0`; tightened to
  `95/90/100/100` in 0.1.0 with real tests.
- Size budget temporarily set to 3 KB gzip; tightened to the 500 B README
  target in 0.1.0.
- Publish workflow exists but trigger is `workflow_dispatch` only — no
  accidental npm release on tag push until 0.1.0.

