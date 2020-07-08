# All about Monocodus configuration: version 1.1.0

This page is designed to provide you with enough information to provide a correct configuration file to **monocodus**. We always encourage you to opt for the most recent version of the config.

## Life before configs
...was dire. It's not especially convenient when you're not able to pick the tools and checks you want to use and disable everything else, as well as to fine-tune the instruments you have chosen. So previously, what we had is all the tools enabled at once, some with pretty arbitrary settings.

!!! Note  
    It's possible you're now using **monocodus** without a config. In this case, there is a default configuration, used instead of the config file in your GitHub repository when you provide none. It's done to provide a reasonable set of tools for the rapid onboarding. Probably you're just getting started and want to know what we can? Or alternatively, you just want a lot of checks, fast.

!!! Note "Differences between 1.0.0 and 1.1.0"
     - Checkers `long_method`, `high_cyclomatic_complexity`, `long_parameter_name` and `long_variable_name` were considered redundant and spammy in their current state and will be turned off until we rework them into something more helpful. You don't need to modify your config, If these checkers were enabled there they will just stop generating comments.
     - Field `checkers` renamed to `file_checkers`. All checks that were previously defined at `checkers` should now be defined there
     - new field `repo_checkers` added in config to store checks related to whole repository. For now there is only one checker — `rust-clippy`
     - new repository checker for `Rust` — `rust-clippy`
     - Checkers `clang-tidy`, `clang-format`, `autopep8`, `fixmyjs`, `php-cs-fixer` and `rust-clippy` are now using config files stored in your repository. For example if you have checker `clang-tidy` enabled and there is `.clang-tidy` file in your repository, it will be used by tool automatically.

As of the time of writing, the default config is equivalent to this one:

```yaml
version: 1.1.0

c:
  file_checkers:
    - name: clang-tidy

c++:
  file_checkers:
    - name: clang-tidy

rust:
  formatter:
    - name: rustfmt

  repo_checkers:
    - name: rust-clippy
```

We maintain our default config at the newest version, so it might be subject to constant cosmetic changes.
For now, the following things are enabled:

* For Rust: _rustfmt_ and _rust-clippy_
* C & C++: _clang-tidy_  

### Why this set?

Previous default config produced a lot of spammy comments according to users feedback so we reduced amount of enabled checkers to ensure monocodus will report only when he has something important to say.

We ended up disabling all formatters, except for `rustfmt`, because most of the time, their default settings were simply inadequate and created a lot of garbage suggestions without individual fine-tuning. Of the checkers, we turned on only `clang-tidy` and `rust-clippy` as they can be useful even without individual configuration.

!!! Tip
    If you are satisfied with default settings, you might not need the config at all.

## Configuration details

In this section, we present all the important details about configuration files and implications they bear.

### Naming, location and format

The configuration file should be written in YAML and named `.monocodus`. You can put it into any directory, but you **must** put one of the configs into your repository root to prevent fallback to the default settings and enable config detection and repository.

Because **monocodus** runs checks on pull requests, it's also important to understand the interaction between the base branch and the branch you're trying to merge in. The config is **always** taken from the base branch (i.e if you're merging `fix` into `master`, **monocodus** will follow the config from `master`). This means that changes in configuration files will only kick in after the merge. We encourage to look at the config change as on a separate task, and merge it separately.

Here are some examples explaining config interactions.

!!! Example  
    You have no config files in your repository. We are going to check your repository according to the default configuration.

!!! Example  
    You have a config file in the repository root, and it's named `monocodus.yaml`. Since **monocodus** searches for the config file by name, it assumes you don't have one and falls back to the default configuration

!!! Example  
    You have `.monocodus` in the repository root, but it's not a valid YAML. The checks are not run, and **monocodus** will comment on what is wrong in the configuration file.

!!! Example  
    You have `.monocodus` in the repository root, it is a valid YAML, but the schema is invalid for the version. The checks are not run, and **monocodus** will comment on what is wrong in the configuration file.

!!! Example  
    You have a valid `.monocodus` in the `tests/` directory, but not in the repository root. **monocodus** won't search for user configs in subdirectories if the root config isn't presented, so the default configuration will be used.

!!! Example  
    You have a valid `.monocodus` in the repository root, but an invalid one in `tests/`. The version from the root will be used everywhere, also  monocodus will explain what's wrong with invalid config in review text.

!!! Example  
    You have a valid `.monocodus` in the repository root, and valid one in `tests/.monocodus`. The version from the root config will be used everywhere, except for `tests/`, while in this directory `tests/.monocodus` and root config will be merged so that options defined in `tests/.monocodus` will override corresponding ones in `.monocodus`.



### Version
The `version` field is always mandatory, and for this version of config, will look like:
```yaml
version: 1.1.0
```

We're using [SchemaVer](https://snowplowanalytics.com/blog/2014/05/13/introducing-schemaver-for-semantic-versioning-of-schemas/) for configuration schema versioning. While similar to SemVer, the differences are the following:  

* SchemaVer version numbers start with `1.0.0`, not `0.1.0`, to highlight that one doesn't change data schema lightly  
* The lowest-level number change (e.g. `1.0.0` to `1.0.1`) indicates _addition_ changes, or changes that maintain full backwards compatibility.  
* The middle number change (e.g. `1.0.1` to `1.1.0`) indicate _revision_ changes, which are, while not large, will break the backwards compatibility for a few config entries, while leaving others intact.  
* The top-level number change (e.g. `1.1.0` to `2.0.0`) indicate _schema_ changes, meaning that with respect to this new version, there is _no_ config file of the previous version which will remain valid.  

We guarantee that you will not have to manually change your configuration every time the revision change is released, due to the config-upgrading mechanism within **monocodus** which allows us to convert all versions to the uniform representation. 

!!! Warning
    In case of some schema changes, when automated conversion is not possible, we may deprecate some of the old schemas and request you to change the configuration file manually to keep up with service updates.

### Ignoring directories and files
If you don't want some of the directories to be checked, you can ignore them like this:  
```yaml
ignore_paths:
  - "/tests"
  - "/utils/[^/]*/gen_[^/][^/]*\\.py"
```

This example will make **monocodus** ignore the whole `tests/` directory, as well as all Python scripts with names starting with `gen_` which are located within any subdirectory of the `utils/` directory. In short, the system treats every line of this parameter like an ordinary regular expression, but you will also need to escape come characters, including `\`. After un-escaping, the second line will look like `/utils/[^/]*/gen_[^/][^/]*\.py` and be ready for compilation into a regex pattern.

!!! Tip
    If you are more used to glob patterns, here's a [short cheatsheet](https://www.linuxjournal.com/content/globbing-and-regex-so-similar-so-different) on differences between them.

You can use this parameter on multiple levels of the **monocodus** config:  

* In the config root, to completely shield some paths from checks  

* Ignore directories by formatter only  

* On the single `file_checker` level, if you want to disable this particular checker for a given set of paths.  
  
!!! Note
    `ignore_paths` won't work at `repo_checkers` because these checkers are made to check whole repository and specific files can't be excluded from the analysis. If tool e.g. `rust-clippy` supports excluding certain files from analysis it should be defined in tool's config file, in this case - `clippy.toml` or `.clippy.toml`

This field is optional, so the config will still be valid if you don't include it.

Want to temporarily disable something for all directories and files? Do this:  

```yaml
ignore_paths:
  - ".*"
```

Splendid!

### Languages
So far, all the checkers are specific to certain programming languages. We currently support:  

* C, key `c`  
* C++, key `c++`  
* Python, key `python`  
* JS, key `javascript`  
* PHP, key `php`  
* Rust, key `rust`  

None of these fields are mandatory. All fields **must** be written in _lowercase_. Strictly latin script, ASCII symbols are implied — not cyrillics or anything else. The fields **must** be located at the config root level, if present.

Example:  
```yaml
version: "1.1.0"

c:
  # language-specific parameters
python:
  # language-specific parameters
```

!!! Important
    The checks are _not_ run for languages left unmentioned.

### Language-specific parameters
The configuration for every programming language follows the same pattern. Here are what fields are available: 

* `ignore_paths` - covered above  
* `formatter`, describing the formatter used for a language. Because normally, multiple formatters produce mutually contradicting results, we only allow to specify one. A detailed description and the list of available formatters can be found [here](formatters/index.md)
* `file_checkers`, a list of file checkers used for a given languages. A detailed description and the list of available file checkers can be found [here](file_checkers/index.md)
* `repo_checkers`, a list of repo-wide checkers used for a given languages. A detailed description and the list of available repository checkers can be found [here](repo_checkers/index.md) 

All three fields are optional.

Here's a simple example:  
```yaml
c:
  ignore_paths:
    - "tests/"
  formatter:
    name: clang-format
  file_checkers:
    - name: clang-tidy
```  
This will run C-related checks for all directories except `tests/`, and will apply formatter `clang-format`, as well as file checker `clang-tidy`.
