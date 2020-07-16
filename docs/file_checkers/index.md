# File checkers

Checkers are various tools meant to help developers find (and sometimes even fix!) potential flaws in code. Different checkers have different purposes and supported languages.

File checkers are checkers which are designed to analyze single files apart from the rest of the project. 

While only one formatter is available for every language at a time, you can have as many checkers as you want, and configure multi-lang ones individually for every language. Detailed description of each one is stated at checkers pages which are presented in the table below.

Monocodus uses following fields to specify file checker settings:

* `name` of the checker. This field is __mandatory__  
* `ignore_paths` — list of directories in which monocodus won't perform formatting check, supports regex.
* `config` — some checkers allow for configuration shortcuts. Availability of this field and list of configurable options for each repo checker are stated at file checkers pages which are presented in table below.

Besides this **monocodus** will use any available tool-specific configs found in your repository. For example if you have specified file checker `clang-tidy` in monocodus config and you have file `.clang-tidy` in your repository, monocodus will use it to perform check.


Here is example of how file checker should be specified in config.

```yaml
c:
  file_checkers:
    - name: clang-tidy
```


# List of available file checkers

|            **Name**             |      **Description**      | **Supported languages** | **Comments** | **Fixes** |
| :-----------------------------: | :-----------------------: | :---------------------: | :----------: | :-------: |
| [**clang-tidy**](clang-tidy.md) | clang-based "linter" tool |       `C`, `C++`        |      No      |    Yes    |