# ESLint Rule Change Review Checklist — PR #21

Review checklist for the lint-rule changes introduced by PR #21 (the ESLint stack
upgrade: eslint 9.31.0 → 10.14.0, typescript-eslint 8.37.0 → 8.72.1,
eslint-plugin-perfectionist 4.15.0 → 5.13.0, @stylistic/eslint-plugin 5.2.0 → 5.10.0,
eslint-plugin-react 7.37.5 → 7.37.5 unchanged).

Companion document: `ESLINT-RULES-DELTA.md` (full catalog diff and methodology).

Scope: **18 items** — 9 newly active rules, 3 newly deprecated rules, 6 new opt-in rules.
No rules were removed, and no rule this repo enables was deprecated.

Each item carries a `**Recommended:**` line — the assistant's starting position for the
discussion. These are proposals, not decisions; each item is reviewed one at a time and
the final action is recorded in the `Decision:` field.

Regardless of the individual decisions, the config decision-log comments in
`build/eslint-config-shared/` will be synced so they stay accurate: the now-preset-enabled
rules get `// Enabled in <preset>` annotations (e.g. `no-unassigned-vars` in `js.mjs`), and
the three deprecated rules get deprecation notes where they are already commented out.

---

## 1. Newly active in this repo (9)

All nine became active with zero config-file changes — purely preset membership shifts
upstream. CI passed with zero violations. For each: keep as-is / tune options / disable.

### eslint core (`eslint:recommended`, 9.31.0 → 10.0.1)

- [x] `preserve-caught-error` (`error`) — Disallow losing originally caught error when re-throwing custom errors. *New rule in ESLint 10; in recommended from day one.*
  - **Recommended:** Accept. Catches a real error-handling anti-pattern (`throw new Error(err.message)` drops the original stack/cause). CI passed clean. If rejected: explicit `"off"` in `js.mjs`.
  - **Decision:** Accept — kept as enabled by `eslint:recommended`; no config change needed.
- [x] `no-unassigned-vars` (`error`) — Disallow `let` or `var` variables that are read but never assigned. *Pre-existing rule, newly added to recommended.*
  - **Recommended:** Accept. Sensible superset of `no-unused-vars`; CI passed clean.
  - **Decision:** Accept — kept as enabled by `eslint:recommended`; no config change needed.
- [x] `no-useless-assignment` (`error`) — Disallow variable assignments when the value is not used. *Pre-existing rule, newly added to recommended.*
  - **Recommended:** Accept. Low-noise dead-assignment check; CI passed clean.
  - **Decision:** Accept — kept as enabled by `eslint:recommended`; no config change needed.

### `@typescript-eslint` (`strictTypeChecked`, 8.37.0 → 8.72.1)

- [x] `no-generated-empty-object-type` (`error`) — Disallow type operations that resolve to the "empty object" type. *New rule.*
  - **Recommended:** Accept. Type-hygiene rule in the strict tier; CI passed clean.
  - **Decision:** Accept — kept as enabled by `strictTypeChecked`; no config change needed.
- [x] `no-unsafe-enum-assignment` (`error`) — Disallow assigning non-enum values to enum typed locations. *New rule.*
  - **Recommended:** Accept. Catches a genuine footgun (non-enum value in an enum-typed location); CI passed clean.
  - **Decision:** Accept — kept as enabled by `strictTypeChecked`; no config change needed.
- [x] `no-useless-default-assignment` (`error`) — Disallow default values that will never be used. *New rule.*
  - **Recommended:** Accept. Dead-code check; CI passed clean.
  - **Decision:** Accept — kept as enabled by `strictTypeChecked`; no config change needed.
- [x] `no-unnecessary-type-conversion` (`error`) — Disallow conversion idioms when they do not change the type or value of the expression. *Pre-existing rule, promoted into the preset in this range.*
  - **Recommended:** Accept. Flags redundant `String(x)`/`Number(x)`/`Boolean(x)`-style noise; CI passed clean.
  - **Decision:** Accept — kept as enabled by `strictTypeChecked`; no config change needed.

The `stylisticTypeChecked` preset is unchanged.

### `eslint-plugin-perfectionist` (`recommended-natural`, 4.15.0 → 5.13.0)

- [x] `sort-export-attributes` (`error`, natural asc) — Enforce sorted export attributes. *New rule.*
  - **Recommended:** Accept. Consistent with the config's stance of sorting import/export syntax; CI passed clean.
  - **Decision:** Accept — kept as enabled by `recommended-natural`; no config change needed.
- [x] `sort-import-attributes` (`error`, natural asc) — Enforce sorted import attributes. *New rule.*
  - **Recommended:** Accept. Same as above.
  - **Decision:** Accept — kept as enabled by `recommended-natural`; no config change needed.

---

## 2. Newly deprecated (3) — confirm no impact

None of these three are enabled in this repo, so no migration is needed. Review = confirm.

- [x] `@typescript-eslint/no-loop-func` — deprecated in favor of the base ESLint rule `no-loop-func`
  - **Recommended:** No action. Already off (commented in `tseslint.mjs`); optionally append a deprecation note to that comment line.
  - **Decision:** Confirmed no impact — rule already off; deprecation note added to the decision log.
- [x] `@typescript-eslint/no-restricted-imports` — deprecated in favor of the base ESLint rule `no-restricted-imports`
  - **Recommended:** No action. Already off (commented in `tseslint.mjs`); optionally append a deprecation note.
  - **Decision:** Confirmed no impact — rule already off; deprecation note added to the decision log.
- [x] `@stylistic/jsx-props-no-multi-spaces` — deprecated in favor of `@stylistic/no-multi-spaces`
  - **Recommended:** No action. Already off (commented in `stylistic.mjs`); optionally append a deprecation note.
  - **Decision:** Confirmed no impact — rule already off; deprecation note added to the decision log. (Note: the replacement `@stylistic/no-multi-spaces` is itself commented out in this config.)

(Pre-existing, unchanged across this upgrade: tseslint `no-empty-interface`,
`no-loss-of-precision`, `no-type-alias`, `no-var-requires`, `prefer-ts-expect-error`,
`sort-type-constituents`, `typedef`; stylistic `jsx-indent`.)

---

## 3. New opt-in rules (6) — decide whether to adopt

Not enabled anywhere in this repo. For each: adopt now / defer / reject.

- [x] `@typescript-eslint/no-unused-private-class-members` — Disallow unused private class members
  - **Recommended:** Adopt. The core rule (now in `eslint:recommended`) only catches ES `#private` fields; this also catches TS `private` members. Quality rule, fits the strict tier. If adopted: explicit `"error"` in `tseslint.mjs`.
  - **Decision:** Adopt — added as an explicit `"error"` entry in `tseslint.mjs`; lint passes with zero violations.
- [x] `@typescript-eslint/strict-void-return` — Disallow passing a value-returning function in a position accepting a void function
  - **Recommended:** Adopt. Type-safety rule that complements the strict tier (catches accidentally-discarded return values, e.g. in callbacks); low noise in practice.
  - **Decision:** Reject — user declined; kept off (commented line) in `tseslint.mjs`.
- [x] `perfectionist/sort-arrays` — Enforce sorted arrays.
  - **Recommended:** Decline. The config explicitly turns off 6 of 20 sort rules (`sort-classes`, `sort-objects`, `sort-object-types`, `sort-modules`, `sort-jsx-props`, `sort-intersection-types`) — the stance is "sort import/export syntax, not code structure".
  - **Decision:** Reject — user confirmed; recorded as a commented line in `perfectionist.mjs`.
- [x] `perfectionist/sort-constructor-parameters` — Enforce sorted constructor parameters.
  - **Recommended:** Decline. Same rationale as `sort-arrays`.
  - **Decision:** Reject — user confirmed; recorded as a commented line in `perfectionist.mjs`.
- [x] `@stylistic/exp-jsx-props-style` *(experimental)* — Enforce consistent line break styles for JSX props
  - **Recommended:** Defer. Experimental (`exp-` prefix, may be renamed or removed); don't pin experimental rules in a shared config. Revisit once stable.
  - **Decision:** Defer — user confirmed; recorded as a commented line in `stylistic.mjs`; revisit when `@stylistic` promotes it to stable.
- [x] `@stylistic/exp-list-style` *(experimental)* — Enforce consistent spacing and line break styles inside brackets.
  - **Recommended:** Defer. Same as above.
  - **Decision:** Defer — user confirmed; recorded as a commented line in `stylistic.mjs`; revisit when `@stylistic` promotes it to stable.

---

## Out of scope for this review

- `eslint-plugin-react` 7.37.5 — version unchanged, no rule delta. (The only repo-side
  React change in PR #21 was `settings.react.version: "19.x"` in `react.mjs`, a
  plugin-internals workaround, not a rule-set change.)
- The 97 rules deprecated in eslint core — identical set in 9.31.0 and 10.14.0 (mostly the
  stylistic split), none enabled in this repo. Full list in `ESLINT-RULES-DELTA.md`.

---

- [x] **Review complete** — all 18 items resolved (2026-11-10)
