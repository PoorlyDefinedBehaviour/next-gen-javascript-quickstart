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
$ yarn start:Watch
$ yarn build:dev
$ yarn build:prod
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
  toString: () => `IO(${g})`,
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
