---
id: version-stable-configuration
title: Configuration File
original_id: configuration
---

Prettier uses [lilconfig](https://github.com/antonk52/lilconfig) for configuration file support. This means you can configure Prettier via (in order of precedence):

- A `"prettier"` key in your `package.json` file.
- A `.prettierrc` file written in JSON or YAML.
- A `.prettierrc.json`, `.prettierrc.yml`, `.prettierrc.yaml`, or `.prettierrc.json5` file.
- A `.prettierrc.js`, or `prettier.config.js` file that exports an object using `export default` or `module.exports` (depends on the [`type`](https://nodejs.org/api/packages.html#type) value in your `package.json`).
- A `.prettierrc.mjs`, or `prettier.config.mjs` file that exports an object using `export default`.
- A `.prettierrc.cjs`, or `prettier.config.cjs` file that exports an object using `module.exports`.
- A `.prettierrc.toml` file.

The configuration file will be resolved starting from the location of the file being formatted, and searching up the file tree until a config file is (or isn’t) found.

Prettier intentionally doesn’t support any kind of global configuration. This is to make sure that when a project is copied to another computer, Prettier’s behavior stays the same. Otherwise, Prettier wouldn’t be able to guarantee that everybody in a team gets the same consistent results.

The options you can use in the configuration file are the same as the [API options](options.md).

## Basic Configuration

JSON:

```json
{
  "trailingComma": "es5",
  "tabWidth": 4,
  "semi": false,
  "singleQuote": true
}
```

JS(ESM):

```js
// prettier.config.js, .prettierrc.js, prettier.config.mjs, or .prettierrc.mjs

/** @type {import("prettier").Config} */
const config = {
  trailingComma: "es5",
  tabWidth: 4,
  semi: false,
  singleQuote: true,
};

export default config;
```

JS(CommonJS):

```js
// prettier.config.js, .prettierrc.js, prettier.config.cjs, or .prettierrc.cjs

/** @type {import("prettier").Config} */
const config = {
  trailingComma: "es5",
  tabWidth: 4,
  semi: false,
  singleQuote: true,
};

module.exports = config;
```

YAML:

```yaml
# .prettierrc or .prettierrc.yaml
trailingComma: "es5"
tabWidth: 4
semi: false
singleQuote: true
```

TOML:

```toml
# .prettierrc.toml
trailingComma = "es5"
tabWidth = 4
semi = false
singleQuote = true
```

## Configuration Overrides

Overrides let you have different configuration for certain file extensions, folders and specific files.

Prettier borrows ESLint’s [override format](https://eslint.org/docs/latest/user-guide/configuring/configuration-files#how-do-overrides-work).

JSON:

```json
{
  "semi": false,
  "overrides": [
    {
      "files": "*.test.js",
      "options": {
        "semi": true
      }
    },
    {
      "files": ["*.html", "legacy/**/*.js"],
      "options": {
        "tabWidth": 4
      }
    }
  ]
}
```

YAML:

```yaml
semi: false
overrides:
  - files: "*.test.js"
    options:
      semi: true
  - files:
      - "*.html"
      - "legacy/**/*.js"
    options:
      tabWidth: 4
```

`files` is required for each override, and may be a string or array of strings. `excludeFiles` may be optionally provided to exclude files for a given rule, and may also be a string or array of strings.

## Sharing configurations

Sharing a Prettier configuration is simple: just publish a module that exports a configuration object, say `@company/prettier-config`, and reference it in your `package.json`:

```json
{
  "name": "my-cool-library",
  "version": "9000.0.1",
  "prettier": "@company/prettier-config"
}
```

If you don’t want to use `package.json`, you can use any of the supported extensions to export a string, e.g. `.prettierrc.json`:

```json
"@company/prettier-config"
```

An example configuration repository is available [here](https://github.com/azz/prettier-config).

> Note: This method does **not** offer a way to _extend_ the configuration to overwrite some properties from the shared configuration. If you need to do that, import the file in a `.prettierrc.js` file and export the modifications, e.g:
>
> ```js
> import companyPrettierConfig from "@company/prettier-config";
>
> export default {
>   ...companyPrettierConfig,
>   semi: false,
> };
> ```

## Setting the [parser](options.md#parser) option

By default, Prettier automatically infers which parser to use based on the input file extension. Combined with `overrides` you can teach Prettier how to parse files it does not recognize.

For example, to get Prettier to format its own `.prettierrc` file, you can do:

```json
{
  "overrides": [
    {
      "files": ".prettierrc",
      "options": { "parser": "json" }
    }
  ]
}
```

You can also switch to the `flow` parser instead of the default `babel` for .js files:

```json
{
  "overrides": [
    {
      "files": "*.js",
      "options": {
        "parser": "flow"
      }
    }
  ]
}
```

**Note:** _Never_ put the `parser` option at the top level of your configuration. _Only_ use it inside `overrides`. Otherwise you effectively disable Prettier’s automatic file extension based parser inference. This forces Prettier to use the parser you specified for _all_ types of files – even when it doesn’t make sense, such as trying to parse a CSS file as JavaScript.

## Configuration Schema

If you’d like a JSON schema to validate your configuration, one is available here: http://json.schemastore.org/prettierrc.

## EditorConfig

If `options.editorconfig` is `true` and an [`.editorconfig` file](https://editorconfig.org/) is in your project, Prettier will parse it and convert its properties to the corresponding Prettier configuration. This configuration will be overridden by `.prettierrc`, etc.

Here’s an annotated description of how different properties map to Prettier’s behavior:

```ini
# Stop the editor from looking for .editorconfig files in the parent directories
# root = true

[*]
# Non-configurable Prettier behaviors
charset = utf-8
insert_final_newline = true
# Caveat: Prettier won’t trim trailing whitespace inside template strings, but your editor might.
# trim_trailing_whitespace = true

# Configurable Prettier behaviors
# (change these if your Prettier config differs)
end_of_line = lf
indent_style = space
indent_size = 2
max_line_length = 80
```

Here’s a copy+paste-ready `.editorconfig` file if you use the default options:

```ini
[*]
charset = utf-8
insert_final_newline = true
end_of_line = lf
indent_style = space
indent_size = 2
max_line_length = 80
```
