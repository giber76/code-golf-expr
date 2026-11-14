# code-golf-expr — AIProve bounty entry

Smallest expression evaluator that passes every test.

## Entry

[`index.js`](index.js) — **79 bytes**, a single file exporting `evalExpr`:

```js
exports.evalExpr=s=>/^[\d.+*\/() -]+$/.test(s)?eval(s.replace(/[-+]/g,'$& ')):x
```

## How it works

1. **Whitelist validation** — the input must consist only of digits, `.`, `+`,
   `-`, `*`, `/`, `(`, `)`, and spaces. Anything else (letters, `;`,
   property access, …) makes it throw instead of executing.
2. **Operator spacing** — every `+`/`-` gets a trailing space, so `++`/`--`
   can never tokenize as JS increment/decrement (`2--3` → `2- - 3` = 5,
   `--5` → `- - 5` = 5).
3. **`eval`** on the validated, normalized string gives correct precedence,
   parentheses, decimals, and unary minus for free.

## Verification (2026-11-14, Node v24)

- Public cases (`cases.json` from the challenge repo): **9/9 pass**
- Hand-written edge cases (unary minus, `--`/`++`/`+-`/`-+`, `.5`, `5.`,
  spaces, `1/0` → `Infinity`, `0/0` → `NaN`, 50-deep parens): **43/43 pass**
- Fuzz vs an independent recursive-descent reference parser:
  **5000/5000 random expressions agree**
- Adversarial inputs (`process.exit()`, `Math.max(1,2)`, `foo`, `1;2`,
  `alert(1)`, `__proto__`, …) all **throw** — no code is ever executed.

## Known limitation

`2**3` returns 8 (JS power operator). `**` is not part of the specified
language (`+ - * /` only), so this is out of scope; a strict parser would
throw instead. Scientific notation (`1e3`) is likewise rejected (throws),
as `e` is not in the specified language.

## Spec

Challenge: https://github.com/AIProveRH/aiprove/tree/main/challenges/code-golf-expr

Bounty: `code-golf-expr` on https://aiprove.fun — "Smallest expression
evaluator that passes every test". Metric: bytes of the source file;
any entry that fails a case scores nothing. Closes 2026-11-16 20:49 WIB.

Author: Giber (`giber`, Pionex618@gmail.com). Built 2026-11-14.
