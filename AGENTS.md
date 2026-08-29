# coc-html repository guidance

## Scope of this file

Keep this file limited to facts and constraints specific to coc-html. General
maintenance, issue handling, upstream synchronization, testing, auditing, and
release procedures belong to the project or installed skills and must not be
duplicated here.

## Repository map

- `src/` is the coc.nvim client. `src/index.ts` owns lazy activation, the
  language client, formatter registration, custom data, semantic tokens, and
  automatic insertion.
- `server/` is the bundled HTML language server, largely derived from the VS
  Code HTML extension. Keep upstream-derived server logic distinct from
  coc.nvim-specific client integration.
- `schemas/` contains published schemas. `package.json` is the public contract
  for activation, configuration, dependencies, and the extension entry point.
- `esbuild.js` creates `lib/index.js` and `lib/server.js`. `lib/` is generated
  and gitignored; edit the TypeScript sources, not bundled output.

## Cross-boundary contracts

- Request names and payloads shared by `src/` and `server/` must remain aligned.
  In particular, keep filesystem requests in both `src/requests.ts` and
  `server/requests.ts`, and keep the custom-data, auto-insert, and semantic-token
  protocol definitions in `src/index.ts` and `server/htmlServer.ts` compatible.
- The manifest activates the extension globally, while `src/index.ts` starts
  the language client only after a configured HTML filetype is present. Preserve
  that lazy-start boundary unless the requested behavior explicitly changes it.
- Formatting is registered by the client according to `html.format.enable`; the
  server is initialized with its formatter capability disabled. Treat those two
  sides as one behavior.

## Build and compatibility facts

- This repository uses Yarn Classic and commits `yarn.lock`; do not introduce a
  second package manager or rewrite the lockfile with one.
- The published coc.nvim compatibility is `^0.0.80`, and the bundle currently
  targets Node.js 12.16. Do not rely on newer coc.nvim APIs or Node.js runtime
  features without an explicit compatibility change.
- `coc.nvim` and `typescript` are external to the bundle. Do not replace
  coc.nvim integration with a `vscode` runtime dependency.
- There is currently no test script in `package.json`; do not report automated
  test coverage unless a real test command has been added and run.
