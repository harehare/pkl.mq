<h1 align="center">pkl.mq</h1>

A [PKL](https://pkl-lang.org/) (Apple's configuration language) parser implemented as an [mq](https://github.com/harehare/mq) module.

## Features

- Line comments (`//`) and block comments (`/* */`)
- Triple-quoted strings (`"""..."""`)
- Type annotations (`key: Type = value`)
- Object blocks (`key { ... }`)
- `List(...)` and `Set(...)` literals
- `Mapping { ["key"] = value }` literals
- Negative numbers
- Module directives (`amends`, `extends`, `module`, `import`) are skipped
- Converts to JSON or Markdown tables

## Installation

Copy `pkl.mq` to your mq module directory, or place it anywhere and reference it with `-L`.

```sh
cp pkl.mq ~/.local/mq/config/
```

### HTTP Import (no local installation needed)

If `mq` was built with the `http-import` feature, you can import directly from GitHub without any local setup:

```sh
mq -I raw 'import "github.com/harehare/pkl.mq" | pkl::pkl_parse(.)' config.pkl
```

Pin to a specific release with `@vX.Y.Z`:

```sh
mq -I raw 'import "github.com/harehare/pkl.mq@v0.1.0" | pkl::pkl_parse(.)' config.pkl
```

## Usage

```sh
mq -L /path/to/modules -I raw \
  'include "pkl" | pkl_parse(.)' config.pkl
```

If you copied it to the mq built-in module directory:

```sh
mq -I raw 'include "pkl" | pkl_parse(.)' config.pkl
```

## API

### `pkl_parse(input)`

Parses a PKL string and returns the corresponding mq value (dict).

| Input type | Output |
|---|---|
| String | Parsed PKL properties as a dict |

### `pkl_stringify(data)`

Converts an mq value back to a PKL string.

| Input type | Output |
|---|---|
| Dict | PKL property assignments |
| Other | PKL literal representation |

### `pkl_to_json(data)`

Converts an mq value to a JSON string.

| Input type | Output |
|---|---|
| Any | JSON string |

### `pkl_to_markdown_table(data)`

Converts an mq value to a Markdown table.

| Input type | Output |
|---|---|
| Dict | Key/Value table |
| Array of dicts | Columns from first element's keys |
| Scalar | Single-column Value table |

## Example

Given `config.pkl`:

```pkl
amends "pkl:base"

// Application configuration
name = "my-app"
version: String = "1.0.0"
port = 8080
debug = false

database {
  host = "localhost"
  port = 5432
}

tags = List("production", "stable")

env = Mapping {
  ["LOG_LEVEL"] = "info"
  ["MAX_CONN"] = "100"
}
```

```sh
mq -L . -I raw 'include "pkl" | pkl_parse(.) | .["name"]' config.pkl
# => "my-app"

mq -L . -I raw 'include "pkl" | pkl_parse(.) | pkl_to_json(.)' config.pkl
# => {"name":"my-app","version":"1.0.0","port":8080,...}

mq -L . -I raw 'include "pkl" | pkl_parse(.) | pkl_to_markdown_table(.)' config.pkl
# => | Key | Value |
#    | --- | --- |
#    | name | my-app |
#    ...
```

## License

MIT
