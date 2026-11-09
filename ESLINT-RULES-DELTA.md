# ESLint Rule Catalog Delta — PR #21

Rule-catalog changes introduced by the ESLint stack upgrade in PR #21
(`06ccdbb` → `c11311e`), organized by rule source.

## Version changes

| Source | Old | New |
|---|---|---|
| `eslint` (core) | 9.31.0 | 10.14.0 |
| `@eslint/js` (the `eslint:recommended` config) | 9.31.0 | 10.0.1 |
| `typescript-eslint` (plugin + parser + presets) | 8.37.0 | 8.72.1 |
| `eslint-plugin-perfectionist` | 4.15.0 | 5.13.0 |
| `@stylistic/eslint-plugin` | 5.2.0 | 5.10.0 |
| `eslint-plugin-react` | 7.37.5 | 7.37.5 (**unchanged — no delta**) |

## Summary

| Source | Catalog | New rules | Removed rules | Newly deprecated | Newly enabled in our config (via preset) |
|---|---|---|---|---|---|
| eslint core | 292 → 293 | 1 | 0 | 0 | 3 |
| @typescript-eslint | 131 → 136 | 5 | 0 | 2 | 4 |
| perfectionist | 20 → 24 | 4 | 0 | 0 | 2 |
| @stylistic | 95 → 97 | 2 (experimental) | 0 | 1 | 0 |
| react | unchanged | 0 | 0 | 0 | 0 |

**No rules were removed anywhere**, and **no rule that this repo enables was deprecated**.
The 9 newly enabled rules all ran in CI with zero violations.

---

## 1. ESLint core (`eslint` 9.31.0 → 10.14.0)

### New rule

- `preserve-caught-error` — new in ESLint 10; **enabled** via `eslint:recommended` (`error`).

### Removed rules

None.

### Deprecated rules

No *new* deprecations. The deprecated set is identical in 9.31.0 and 10.14.0 (97 long-standing
deprecations — mostly formatting rules superseded by `@stylistic/*`, plus old `node/*`-era rules).
Full list in the appendix.

### Newly enabled via `eslint:recommended` (`@eslint/js` 9.31.0 → 10.0.1)

| Rule | Severity | Notes |
|---|---|---|
| `no-unassigned-vars` | error | Pre-existing core rule; newly added to recommended |
| `no-useless-assignment` | error | Pre-existing core rule; newly added to recommended |
| `preserve-caught-error` | error | New rule; in recommended from day one |

---

## 2. `@typescript-eslint` (8.37.0 → 8.72.1)

### New rules (5)

| Rule | Enabled in our config? |
|---|---|
| `no-generated-empty-object-type` | Yes — `strictTypeChecked` preset (`error`) |
| `no-unsafe-enum-assignment` | Yes — `strictTypeChecked` preset (`error`) |
| `no-useless-default-assignment` | Yes — `strictTypeChecked` preset (`error`) |
| `no-unused-private-class-members` | No — opt-in only |
| `strict-void-return` | No — opt-in only |

### Newly added to the `strictTypeChecked` preset (pre-existing rule)

| Rule | Severity | Notes |
|---|---|---|
| `no-unnecessary-type-conversion` | error | Existed since well before 8.37; promoted into the preset in the 8.37 → 8.72 range |

The `stylisticTypeChecked` preset is unchanged.

### Removed rules

None.

### Newly deprecated (in favor of the base ESLint rules)

| Rule | Deprecation |
|---|---|
| `no-loop-func` | use base `no-loop-func` |
| `no-restricted-imports` | use base `no-restricted-imports` |

Neither is enabled in this repo (both are commented out in `js.mjs`), so no action needed.

### Previously already deprecated (unchanged across the upgrade)

`no-empty-interface`, `no-loss-of-precision`, `no-type-alias`, `no-var-requires`,
`prefer-ts-expect-error`, `sort-type-constituents`, `typedef`

---

## 3. `eslint-plugin-perfectionist` (4.15.0 → 5.13.0)

### New rules (4)

| Rule | Enabled in our config? |
|---|---|
| `sort-export-attributes` | Yes — `recommended-natural` preset (`error`, natural asc) |
| `sort-import-attributes` | Yes — `recommended-natural` preset (`error`, natural asc) |
| `sort-arrays` | No — opt-in only |
| `sort-constructor-parameters` | No — opt-in only |

### Removed rules

None (all 20 v4 rules remain).

### Deprecated rules

None.

---

## 4. `@stylistic/eslint-plugin` (5.2.0 → 5.10.0)

### New rules (2, both experimental)

Both are registered with the `exp-` prefix and are not in the `recommended` preset:

| Rule | Enabled in our config? |
|---|---|
| `exp-jsx-props-style` | No — experimental, opt-in only |
| `exp-list-style` | No — experimental, opt-in only |

### Removed rules

None.

### Newly deprecated

| Rule | Deprecation |
|---|---|
| `jsx-props-no-multi-spaces` | use `@stylistic/no-multi-spaces` instead |

Not enabled in this repo. Pre-existing deprecation (unchanged): `jsx-indent` → use `@stylistic/indent`.

### Preset changes

`recommended` rule membership is unchanged. The default options of `@stylistic/indent` changed
slightly (TS-specific `ignoredNodes` entries were dropped upstream), which is moot here because
`stylistic.mjs` overrides `indent` explicitly (`["error", 4]`).

---

## 5. `eslint-plugin-react` (7.37.5 → 7.37.5)

No version change — no catalog, preset, or deprecation delta. (The only repo-side change for
React in PR #21 was the `settings.react.version: "19.x"` workaround in `react.mjs` for the
ESLint 10 removal of `context.getFilename()`; that affects plugin internals, not the rule set.)

---

## What actually changed in this repo's effective ruleset

9 rules became active with no config-file changes (all via presets); CI passed with zero
violations on all of them:

```
preserve-caught-error                     (core, via eslint:recommended)
no-unassigned-vars                        (core, via eslint:recommended)
no-useless-assignment                     (core, via eslint:recommended)
@typescript-eslint/no-generated-empty-object-type   (via strictTypeChecked)
@typescript-eslint/no-unnecessary-type-conversion   (via strictTypeChecked)
@typescript-eslint/no-unsafe-enum-assignment        (via strictTypeChecked)
@typescript-eslint/no-useless-default-assignment    (via strictTypeChecked)
perfectionist/sort-export-attributes      (via recommended-natural)
perfectionist/sort-import-attributes      (via recommended-natural)
```

Rules deprecated but enabled here: **none**.
Rules removed that were enabled here: **none**.

New opt-in rules available if we ever want them (6):

```
@typescript-eslint/no-unused-private-class-members
@typescript-eslint/strict-void-return
perfectionist/sort-arrays
perfectionist/sort-constructor-parameters
@stylistic/exp-jsx-props-style   (experimental)
@stylistic/exp-list-style        (experimental)
```

---

## Methodology

- **Catalog diff**: runtime introspection of each package's registered rule registry
  (`plugin.rules`) with the old and new stacks installed side by side
  (eslint core: rule files in `lib/rules/`, which is the registry's directory scan).
- **Preset membership**: runtime introspection of each version's config exports
  (`js.configs.recommended`, `tseslint.configs.{strictTypeChecked,stylisticTypeChecked}`,
  `stylistic.configs.recommended`, `perfectionist.configs["recommended-natural"]`) with the old
  and new stacks installed side by side.
- **Deprecation flags**: rule metadata in the published dist code
  (`docs.deprecated.deprecatedSince` / `docs.description`).
- Config-file delta between `06ccdbb` and `c11311e` was limited to
  `build/eslint-config-shared/package.json` and `react.mjs`, so every effective change above
  comes from the plugin versions themselves.

<details>
<summary>Appendix: 97 rules deprecated in eslint core 10.14.0 (identical in 9.31.0)</summary>

```
array-bracket-newline            max-statements-per-line
array-bracket-spacing            multiline-comment-style
array-element-newline            multiline-ternary
arrow-parens                     new-parens
arrow-spacing                    newline-after-var
block-spacing                    newline-before-return
brace-style                      newline-per-chained-call
callback-return                  no-buffer-constructor
comma-dangle                     no-catch-shadow
comma-spacing                    no-confusing-arrow
comma-style                      no-extra-parens
computed-property-spacing        no-extra-semi
dot-location                     no-floating-decimal
eol-last                         no-mixed-operators
func-call-spacing                no-mixed-requires
function-call-argument-newline   no-mixed-spaces-and-tabs
function-paren-newline           no-multi-spaces
generator-star-spacing           no-multiple-empty-lines
global-require                   no-native-reassign
handle-callback-err              no-negated-in-lhs
id-blacklist                     no-new-object
implicit-arrow-linebreak         no-new-require
indent                           no-new-symbol
indent-legacy                    no-path-concat
jsx-quotes                       no-process-env
key-spacing                      no-process-exit
keyword-spacing                  no-restricted-modules
line-comment-position            no-return-await
linebreak-style                  no-spaced-func
lines-around-comment             no-sync
lines-around-directive           no-tabs
lines-between-class-members      no-trailing-spaces
max-len                          no-whitespace-before-property
nonblock-statement-body-position operator-linebreak
object-curly-newline             padded-blocks
object-curly-spacing             padding-line-between-statements
object-property-newline          prefer-reflect
one-var-declaration-per-line     quote-props
                                 quotes
```

(The formatting above is a two-column rendering of the plain list; the canonical flat list:
`array-bracket-newline, array-bracket-spacing, array-element-newline, arrow-parens, arrow-spacing, block-spacing, brace-style, callback-return, comma-dangle, comma-spacing, comma-style, computed-property-spacing, dot-location, eol-last, func-call-spacing, function-call-argument-newline, function-paren-newline, generator-star-spacing, global-require, handle-callback-err, id-blacklist, implicit-arrow-linebreak, indent, indent-legacy, jsx-quotes, key-spacing, keyword-spacing, line-comment-position, linebreak-style, lines-around-comment, lines-around-directive, lines-between-class-members, max-len, max-statements-per-line, multiline-comment-style, multiline-ternary, new-parens, newline-after-var, newline-before-return, newline-per-chained-call, no-buffer-constructor, no-catch-shadow, no-confusing-arrow, no-extra-parens, no-extra-semi, no-floating-decimal, no-mixed-operators, no-mixed-requires, no-mixed-spaces-and-tabs, no-multi-spaces, no-multiple-empty-lines, no-native-reassign, no-negated-in-lhs, no-new-object, no-new-require, no-new-symbol, no-path-concat, no-process-env, no-process-exit, no-restricted-modules, no-return-await, no-spaced-func, no-sync, no-tabs, no-trailing-spaces, no-whitespace-before-property, nonblock-statement-body-position, object-curly-newline, object-curly-spacing, object-property-newline, one-var-declaration-per-line, operator-linebreak, padded-blocks, padding-line-between-statements, prefer-reflect, quote-props, quotes, rest-spread-spacing, semi, semi-spacing, semi-style, space-before-blocks, space-before-function-paren, space-in-parens, space-infix-ops, space-unary-ops, spaced-comment, switch-colon-spacing, template-curly-spacing, template-tag-spacing, wrap-iife, wrap-regex, yield-star-spacing`.)

Most are deprecated in favor of their `@stylistic/*` equivalents (the stylistic split) or
`eslint-plugin-n` equivalents (the `node/*`-era rules). None are enabled in this repo's config.

</details>
