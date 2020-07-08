# Formatters

Formatters are tools that monocodus uses to ensure consistency in the project's code style. Because multiple formatters produce mutually contradicting results, we allow specifying only one formatter per language.

Formatters should be specified at language level in special field `formatter`.

Monocodus uses following fields to specify formatter settings:

* `name` of the formatter. This field is __mandatory__. The names are _case-insensitive_, i.e `FIXMYJS` and `FixMyJS` map to `fixmyjs` during processing.
* `ignore_paths` — list of directories in which monocodus won't perform formatting check, supports regex.
* `config` — some formatters allow for configuration shortcuts. Availability of this field and list of configurable options for each formatter are stated at formatters pages which are presented in table below.

Besides this **monocodus** will use any tool-specific configs found in your repository. For example if you have specified formatter `clang-format` in monocodus config and you have file `.clang-format` in your repository, monocodus will use it to perform style check.

Here is example of how formatter should be specified in config.

```yaml
c:
  formatter:
    name: clang-format
    ignore_paths:
      - "tests/"
      - "/utils/[^/]*/gen_[^/][^/]*\\.c"
    config:
      style: LLVM
``` 

This example will make **monocodus** check formatting of files in `C` language using tool `clang-format` with predefined code-style `LLVM` while ignoring the whole `tests/` directory, as well as all `C` files with names starting with `gen_` which are located within any subdirectory of the `utils/` directory. In short, the system treats every line of this parameter like an ordinary regular expression, but you will also need to escape come characters, including `\`. After un-escaping, the second line will look like `/utils/[^/]*/gen_[^/][^/]*\.py` and be ready for compilation into a regex pattern.


# List of available formatters

|            **Name**             | **Supported languages** |
| :-----------------------------: | :---------------------: |
| [clang-format](clang-format.md) |         C, C++          |
|     [AutoPEP8](autopep8.md)     |         Python          |
|      [FixMyJS](fixmyjs.md)      |       javascript        |
| [PHP-CS-FIXER](php-cs-fixer.md) |           PHP           |
|      [rustfmt](rustfmt.md)      |          Rust           |