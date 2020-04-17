```console
WWWWWW||WWWWWW
 W W W||W W W
      ||
    ( OO )__________
     /  |           \
    /o o|    MIT     \
    \___/||_||__||_|| *
         || ||  || ||
        _||_|| _||_||
       (__|__|(__|__|


$ yarn
$ yarn start
$ yarn start:watch
$ yarn build:dev
$ yarn build:prod
```

```js
import { Left, Right } from "../data/either"

// String ~ [Char]
const Iso = (to, from) => ({
  to,
  from,
})

const chars = Iso(
  s => s.split(""),
  s => s.join("")
)

"hello world" |> chars.to |> chars.from |> console.log

const isNullOrUndefined = x => x === null || x === undefined

// [a] ~ Either null a
const singleton = Iso(
  either =>
    either.fold(
      () => [],
      x => [x]
    ),
  ([x]) => (isNullOrUndefined(x) ? Left(x) : Right(x))
)

const toString = x => x.toString()

const map = f => x => x.map(f)

const filterEither = (either, predicate) =>
  singleton.to(either).filter(predicate) |> singleton.from

filterEither(Right("hello"), x => /h/.test(x))
  |> map(s => s.toUpperCase())
  |> toString
  |> console.log
```

```js
const Right = x => ({
  map: f => Right(f(x)),
  fold: (_f, g) => g(x),
  chain: f => f(x),
  ap: fx => fx.map(x),
  inspect: () => `Right(${x})`,
})

const Left = x => ({
  map: _f => Left(x),
  fold: (f, _g) => f(x),
  chain: _f => Left(x),
  ap: _f => Left(x),
  inspect: () => `Left(${x})`,
})

const Either = Right

Either.of = x => Right(x)

const fromNullable = x => (x === null || x === undefined ? Left(x) : Right(x))

const IO = g => ({
  map: f => IO(() => f(g())),
  fold: () => g(),
  inspect: () => `IO(${g})`,
})

IO.of = x => IO(() => x)

const prop = property => object => object[property]

const flip = f => a => b => f(b)(a)

const findColor = name =>
  name
  |> flip(prop)({ red: "#ff4444", blue: "#3b5998", yellow: "#fff68f" })
  |> fromNullable

const log = x => IO(() => console.log(x))

const foundRed =
  findColor("red").fold(
    () => "color not found",
    color => `color found ${color}`
  ) |> log

const foundGreen =
  findColor("green").fold(
    () => "color not found",
    color => `color found ${color}`
  ) |> log

foundRed.fold()
foundGreen.fold()
```
