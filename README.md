# bs-decode

Decode JSON values into structured ReasonML and OCaml types. Inspired by Elm's [Json.Decode](https://package.elm-lang.org/packages/elm-lang/core/5.1.1/Json-Decode) and the [Decode Pipeline](https://package.elm-lang.org/packages/NoRedInk/elm-decode-pipeline/3.0.1/Json-Decode-Pipeline), `bs-decode` is an alternative to [bs-json](https://github.com/glennsl/bs-json) that focuses on structured, type-safe error handling, rather than exceptions. Additionally, `bs-decode` collects up _everything_ that went wrong while parsing the JSON, rather than failing on the first error.

## Installation

**Install via npm:**

`npm install --save bs-decode`

**Update your bsconfig.json**

```
"bs-dependencies": [
  "bs-decode"
],
```


## Usage

The following is available to give you an idea of how the library works, but [the complete documentation](https://mlms13.github.io/bs-decode/docs/what-and-why) will probably be more useful if you want to write your own decoders.

```reason
/* imagine you have a `user` type and `make` function to construct one */
type user = {
  name: string,
  age: int,
  isAdmin: bool,
  lastLogin: option(Js.Date.t)
};

let make = (name, age, isAdmin, lastLogin) =>
  { name, age, isAdmin, lastLogin };

/**
 * Given a JSON value that looks like:
 * { "name": "Michael", "age": 32, "isAdmin": true }
 *
 * you can write a function to convert this JSON into a value of type `user`
 */
module D = Decode.AsResult.OfParseError; /* module alias for brevity */

let decode = json =>
  D.Pipeline.(
    succeed(make)
    |> field("name", D.string)
    |> field("age", D.intFromNumber)
    |> field("isAdmin", D.boolean)
    |> optionalField("lastLogin", D.date)
    |> run(json)
  );

let myUser = decode(json); /* Belt.Result.Ok({...}) */
```

## Contributing

1. Fork and clone this repository
2. `npm install`
3. Add features and tests
4. `npm run test`

A note on project structure:

- `DecodeBase.re` is where most of the actual functionality lives
- `Decode_As*.re` define the actual implementations, but those are minimal
- `Decode_ParseError.re` defines the error type useful for Results, as well as a collection of helpers to work with Results of that kind.

## License

Released under the MIT license. See `LICENSE`.
