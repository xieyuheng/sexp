# S-expression

> Syntax without representation is tyranny.
>
> -- Sussman's mantra

An implementation of [S-expression](https://en.wikipedia.org/wiki/S-expression) in JavaScript.

## Installation

```
npm i @xieyuheng/sexp
```

## Examples

Parsing lambda calculus expressions:

```
<exp> = <variable>
      | (lambda (<name> ...) <exp>)
      | (<exp> <exp> ...)
```

Example from from [**@xieyuheng/lambda.sexp**](https://github.com/xieyuheng/lambda.sexp):

- See [**src/lang/parser/**](https://github.com/xieyuheng/lambda.sexp/tree/master/src/lang/parser) for the complete example.

```typescript
import { cons, match, matchList, matchSymbol, Sexp, v } from "@xieyuheng/sexp"
import { Exp } from "../exp"
import * as Exps from "../exps"

export function matchExp(sexp: Sexp): Exp {
  return match<Exp>(sexp, [
    [
      ["lambda", v("names"), v("exp")],
      ({ names, exp }) =>
        matchList(names, matchSymbol).reduceRight(
          (fn, name) => new Exps.Fn(name, fn),
          matchExp(exp),
        ),
    ],
    [
      cons(v("target"), v("args")),
      ({ target, args }) =>
        matchList(args, matchExp).reduce(
          (result, arg) => new Exps.Ap(result, arg),
          matchExp(target),
        ),
    ],
    [v("name"), ({ name }) => new Exps.Var(matchSymbol(name))],
  ])
}
```

## Development

```sh
npm install      # Install dependencies
npm run build    # Compile `src/` to `lib/`
npm run format   # Format the code
npm run test     # Run test
```

## License

[GPLv3](LICENSE)
