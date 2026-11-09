# ESLint Rule Change Review Checklist — PR #21

Review checklist for the lint-rule changes introduced by PR #21 (the ESLint stack
upgrade: eslint 9.31.0 → 10.14.0, typescript-eslint 8.37.0 → 8.72.1,
eslint-plugin-perfectionist 4.15.0 → 5.13.0, @stylistic/eslint-plugin 5.2.0 → 5.10.0,
eslint-plugin-react 7.37.5 → 7.37.5 unchanged).

Companion document: `ESLINT-RULES-DELTA.md` (full catalog diff and methodology).

Scope: **18 items** — 9 newly active rules, 3 newly deprecated rules, 6 new opt-in rules.
No rules were removed, and no rule this repo enables was deprecated.

---

## 1. Newly active in this repo (9)

All nine became active with zero config-file changes — purely preset membership shifts
upstream. CI passed with zero violations. For each: keep as-is / tune options / disable.

### eslint core (`eslint:recommended`, 9.31.0 → 10.0.1)

- [ ] `preserve-caught-error` (`error`) — Disallow losing originally caught error when re-throwing custom errors. *New rule in ESLint 10; in recommended from day one.*
- [ ] `no-unassigned-vars` (`error`) — Disallow `let` or `var` variables that are read but never assigned. *Pre-existing rule, newly added to recommended.*
- [ ] `no-useless-assignment` (`error`) — Disallow variable assignments when the value is not used. *Pre-existing rule, newly added to recommended.*

### `@typescript-eslint` (`strictTypeChecked`, 8.37.0 → 8.72.1)

- [ ] `no-generated-empty-object-type` (`error`) — Disallow type operations that resolve to the "empty object" type. *New rule.*
- [ ] `no-unsafe-enum-assignment` (`error`) — Disallow assigning non-enum values to enum typed locations. *New rule.*
- [ ] `no-useless-default-assignment` (`error`) — Disallow default values that will never be used. *New rule.*
- [ ] `no-unnecessary-type-conversion` (`error`) — Disallow conversion idioms when they do not change the type or value of the expression. *Pre-existing rule, promoted into the preset in this range.*

The `stylisticTypeChecked` preset is unchanged.

### `eslint-plugin-perfectionist` (`recommended-natural`, 4.15.0 → 5.13.0)

- [ ] `sort-export-attributes` (`error`, natural asc) — Enforce sorted export attributes. *New rule.*
- [ ] `sort-import-attributes` (`error`, natural asc) — Enforce sorted import attributes. *New rule.*

---

## 2. Newly deprecated (3) — confirm no impact

None of these three are enabled in this repo, so no migration is needed. Review = confirm.

- [ ] `@typescript-eslint/no-loop-func` — deprecated in favor of the base ESLint rule `no-loop-func`
- [ ] `@typescript-eslint/no-restricted-imports` — deprecated in favor of the base ESLint rule `no-restricted-imports`
- [ ] `@stylistic/jsx-props-no-multi-spaces` — deprecated in favor of `@stylistic/no-multi-spaces`

(Pre-existing, unchanged across this upgrade: tseslint `no-empty-interface`,
`no-loss-of-precision`, `no-type-alias`, `no-var-requires`, `prefer-ts-expect-error`,
`sort-type-constituents`, `typedef`; stylistic `jsx-indent`.)

---

## 3. New opt-in rules (6) — decide whether to adopt

Not enabled anywhere in this repo. For each: adopt now / defer / reject.

- [ ] `@typescript-eslint/no-unused-private-class-members` — Disallow unused private class members
- [ ] `@typescript-eslint/strict-void-return` — Disallow passing a value-returning function in a position accepting a void function
- [ ] `perfectionist/sort-arrays` — Enforce sorted arrays.
- [ ] `perfectionist/sort-constructor-parameters` — Enforce sorted constructor parameters.
- [ ] `@stylistic/exp-jsx-props-style` *(experimental)* — Enforce consistent line break styles for JSX props
- [ ] `@stylistic/exp-list-style` *(experimental)* — Enforce consistent spacing and line break styles inside brackets.

---

## Out of scope for this review

- `eslint-plugin-react` 7.37.5 — version unchanged, no rule delta. (The only repo-side
  React change in PR #21 was `settings.react.version: "19.x"` in `react.mjs`, a
  plugin-internals workaround, not a rule-set change.)
- The 97 rules deprecated in eslint core — identical set in 9.31.0 and 10.14.0 (mostly the
  stylistic split), none enabled in this repo. Full list in `ESLINT-RULES-DELTA.md`.

---

- [ ] **Review complete** — all items above resolved
