Checkers are various tools meant to help developers find (and sometimes even fix!) potential flaws in code. Different checkers have different purposes and supported languages.
# Repository checkers

Repository checkers are complex checkers which analyze whole repository content to find issues which are out of the scope of single file analysis.

While only one formatter is available for every language at a time, you can have as many repository checkers as you want, and configure multi-lang individually for every language. Detailed description of each one is stated at checkers pages which are presented in the table below.

Monocodus uses following fields to specify file checker settings:

!!! Note
    `ignore_paths` won't work at `repo_checkers` because these checkers are made to check whole repository and specific files can't be excluded from the analysis. If tool e.g. `rust-clippy` supports excluding certain files from analysis it should be defined in tool's config file, in this case — `clippy.toml` or `.clippy.toml`

* `name` of the checker. This field is __mandatory__  
* `project_roots` — list of paths to projects contained in repository relatively to repository root. Sometimes single repository contains more than one project, which are defined by some project-level files like `cargo.toml` in `Rust`. Some repository checkers work more on a project-level than on repository level so you need to show monocodus where are the directories containing projects you want to check. Default value of this parameter is `*` which means that monocodus repository as containing single project in root directory. You can determine if repository checker uses this feature by attribute `Requires project_roots` on checker's page.
* `config` — some checkers allow for configuration shortcuts. Availability of this field and list of configurable options for each repo checker are stated at checkers pages which are presented in table below.

Besides this **monocodus** will use any available tool-specific configs found in your repository. For example if you have specified file checker `rust-clippy` in monocodus config and you have Clippy configuration options defined at file `clippy.toml` in your repository, monocodus will use it to perform check.

Here is example of how repo checker should be specified in config.

```yaml
rust:
  repo_checkers:
    - name: rust-clippy
      project_roots:
        - project_1
        - project_2
```


# List of available repostiory checkers

|             **Name**              |                               **Description**                               | **Supported languages** | **Comments** | **Fixes** |
| :-------------------------------: | :-------------------------------------------------------------------------: | :---------------------: | :----------: | :-------: |
| [**rust-clippy**](rust-clippy.md) | A collection of lints to catch common mistakes and improve your  Rust code. |         `Rust`          |     Yes      |    No     |