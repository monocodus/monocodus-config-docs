# All about Monocodus configuration: version 1.0.0

This page is designed to provide you with enough information to provide a correct configuration file to **monocodus**. We always encourage you to opt for the most recent version of the config.

## Life before configs
...was dire. It's not especially convenient when you're not able to pick the tools and checks you want to use and disable everything else, as well as to fine-tune the instruments you have chosen. So previously, what we had is all the tools enabled at once, some with pretty arbitrary settings.

Now, it's still possible you're using **monocodus** without a config. In this case, there is something called default config, which is used instead of the config file in your GitHub repository when you provide none. It's objective is to provide a more or less optimal set of tools for the rapid onboarding. Probably you're just getting started and want to know what we can? Or alternatively, you just want a lot of checks, fast.

Either way, as of the time of writing, the default config is equivalent to this one:
 ```yaml
version: 1.0.0

c:
  checkers:
    - name: clang-tidy
    - name: long_method
      config:
        threshold: 70
    - name: high_cyclomatic_complexity
      config:
        threshold: 40
    - name: long_parameter_list
      config:
        threshold: 10

c++:
  checkers:
    - name: clang-tidy
    - name: long_method
      config:
        threshold: 70
    - name: high_cyclomatic_complexity
      config:
        threshold: 40
    - name: long_parameter_list
      config:
        threshold: 10

python:
  checkers:
    - name: long_method
      config:
        threshold: 50
    - name: high_cyclomatic_complexity
      config:
        threshold: 20
    - name: long_parameter_list
      config:
        threshold: 6
    - name: long_variable_name
      config:
        threshold: 25

javascript:
  checkers:
    - name: long_method
      config:
        threshold: 50
    - name: high_cyclomatic_complexity
      config:
        threshold: 20
    - name: long_parameter_list
      config:
        threshold: 6
    - name: long_variable_name
      config:
        threshold: 25

rust:
  formatter:
    name: rustfmt
 ```
 We maintain our default config at the newest version, so it might be subject to constant cosmetic changes.
 Regardless, for now, the following things are enabled:
 - For Rust, _rustfmt_ is enabled
 - C & C++ code is checked via _clang-tidy_
 - We detect _long methods and functions_, _high cyclomatic complexity_, and _long parameter lists_ in function signatures for C, C++, Python, and JS code, with respective thresholds defining what is considered to be long or high
 - We detect _long variable names_ for Python and JS code, with threshold of 25 symbols
 - All thresholds are inclusive, i.e if the threshold for `long_method` is 25, you will not be getting notifications for functions 25 lines long, but will for those 26 or more lines long

 ### Why this set?
 We were thinking long and hard about the set of tools anabled by default. There were options "enable all", "disable all", and anything in between. Yet enabling everything at once proved to be spammy, not to mention how some checkers directly contradicted the others. Disabling everything would rob those who are trying the tool out, because one shouldn't have to go through the whole documentation to try a few things.

 We ended up disabling all formatters, except for `rustfmt`, because most of the time, their default settings were simply inadequate and created a lot of garbage suggestions without individual fine-tuning. At the same time, we left most of checkers not involved in formatting enabled, with relaxed thresholds, as most of them are more or less universally useful, but the default settings shouldn't be too strict. This way the new users will be able to feel the capability of **monocodus** while not being overwhelmed by excessive fix suggestions.

 So if you are satisfied with default settings, you might not need the config at all.

 ## Configuration details

 In this section, we present all the important details about configuration files and implications they bear.

### Naming, location and format

The configuration file should be written in YAML and named `.monocodus`. You can put it into any directory (as of the time of writing, the config overriding implementation is _in progress_ and not yet available), but you **must** put one of the configs into your repository root to prevent fallback to the default settings and enable config detection and repository.

Because **monocodus** runs checks on pull requests, it's also important to understand the interaction between the base branch and the branch you're trying to merge in. The config is **always** taken from the base branch (i.e if you're merging `fix` into `master`, **monocodus** will follow the config from `master`). This means that changes in configuration files will only kick in after the merge. We encourage to look at the config change as on a separate task, and merge it separately.

We assume that errors are made unintentionally, and hence store last valid config for a given branch and location. If the new configuration file is broken, **monocodus** will retrieve the last valid config to perform the checks

**Example 1.** You have no config files in your repository. We are going to check your repository according to the default configuration.

**Example 2.** You have a config file in the repository root, and it's named `monocodus.yaml`. Since **monocodus** searches for the config file by name, it assumes you don't have one and falls back to the default configuration

**Example 3.** You have `.monocodus` in the repository root, but it's not a valid YAML. The checks are not run, and **monocodus** will comment on what is wrong in the configuration file.

**Example 4.** You have `.monocodus` in the repository root, it is a valid YAML, but the schema is invalid for the version. The checks are not run, and **monocodus** will comment on what is wrong in the configuration file.

**Example 5.** You have a valid `.monocodus` in the `tests/` directory, but not in the repository root. We currently aren't searching for the config in places other than a repository root just yet, so the default configuration will be used.

**Example 6.** You have a valid `.monocodus` in the repository root, but an invalid one in `tests/`. The version from the root will be used everywhere.

**Example 7.** You previously had a valid `.monocodus` file, but now it is invalid. The last valid config for the respective branch is going to be used. In addition, **monocodus** will comment on errors in the new config file.

### Version
The `version` field is always mandatory, and for this version of config, will look like:
```yaml
version: 1.0.0
```

We're using [SchemaVer](https://snowplowanalytics.com/blog/2014/05/13/introducing-schemaver-for-semantic-versioning-of-schemas/) for configuration schema versioning. While similar to SemVer, the differences are the following:
- SchemaVer version numbers start with `1.0.0`, not `0.1.0`, to highlight that one doesn't change data schema lightly
- The lowest-level number change (e.g. `1.0.0` to `1.0.1`) indicates _addition_ changes, or changes that maintain full backwards compatibility.
- The middle number change (e.g. `1.0.1` to `1.1.0`) indicate _revision_ changes, which are, while not large, will break the backwards compatibility for a few config entries, while leaving others intact.
- The top-level number change (e.g. `1.1.0` to `2.0.0`) indicate _schema_ changes, meaning that with respect to this new version, there is _no_ config file of the previous version which will remain valid.

We guarantee that you will not have to manually change your configuration every time the revision change is released, due to the config-upgrading mechanism within **monocodus** which allows us to convert all versions to the uniform representation. In case of some schema changes, when automated conversion is not possible, we may deprecate some of the old schemas and request you to change the configuration file manually to keep up with service updates.

### Ignoring directories and files
If you don't want some of the directories to be checked, you can ignore them like this:
```yaml
ignore_paths:
    - "/tests"
    - "/utils/[^/]*/gen_[^/][^/]*\\.py"
```

This example will make **monocodus** ignore the whole `tests/` directory, as well as all Python scripts with names starting with `gen_` which are located within any subdirectory of the `utils/` directory. In short, the system treats every line of this parameter like an ordinary regular expression, but you will also need to escape come characters, including `\`. After un-escaping, the second line will look like `/utils/[^/]*/gen_[^/][^/]*\.py` and be ready for compilation into a regex pattern.

If you are more used to glob patterns, here's a [short cheatsheet](https://www.linuxjournal.com/content/globbing-and-regex-so-similar-so-different) on differences between them.

You can use this parameter on multiple levels of the **monocodus** config:
- In the config root, to completely shield some paths from checks
- Ignore directories by formatter only
- On the single checker level, if you want to disable this particular checker for a given set of paths.

This field is optional, so the config will still be valid if you don't include it.

And if you want to temporarily disable something for all directories and files? Do this:
```yaml
ignore_paths:
    - ".*"
```

Splendid!

### Languages
So far, all the checkers are specific to certain programming languages. We currently support:
- C, key `c`
- C++, key `c++`
- Python, key `python`
- JS, key `javascript`
- PHP, key `php`
- Rust, key `rust`

None of these fields are mandatory. All fields **must** be written in _lowercase_. Strictly latin script, ASCII symbols are implied — not cyrillics or anything else. The fields **must** be located at the config root level, if present.

Example:
```yaml
version: "1.0.0"

c:
    # language-specific parameters
python:
    # language-specific parameters
```

The checks are _not_ run for languages left unmentioned.

### Language-specific parameters
The configuration for every programming language follows the same pattern. Here are what fields are available:
- `ignore_paths`, covered above
- `formatter`, describing the formatter used for a language. Because normally, multiple formatters produce mutually contradicting results, we only allow to specify one
- `checkers`, a list of checkers used for a given languages

All three fields are optional.

Here's a simple example:
```yaml
c:
    ignore_paths:
        - "tests/"
    formatter:
        name: clang-format
    checkers:
        - name: long_method
          config:
            threshold: 35
```
This will run C-related checks for all directories except `tests/`, and will apply the default configuration of `clang-format`, as well as check function lengths and request changes for all the functions longer than 35 lines.

### Configuring the formatter
As said above, you can configure one formatter per language, according to the specific scheme.
- `name` of the formatter. This field is __mandatory__
- `ignore_paths`, discussed in detail above — optional
- `config` — some formatters allow for configuration shortcuts. This field is required for some formatters, optional for some more, and for others, it is an error to include it.

We have a curated list of formatters available, and it's being continuously expanded. Available now are:
- `autopep8` for Python
- `fixmyjs` for JS
- `rustfmt` for Rust
- `php-cs-fixer` for PHP
- `clang-format` for C and C++

As of the time of writing, this is a complete list of accepted values for the `name` field. The names are _case-insensitive_, i.e `FIXMYJS` and `FixMyJS` map to `fixmyjs` during processing.

So far, only `clang-format` has a `config` entry. It has the single required field `style`, which can be one of the following:
- `LLVM`
- `Google`
- `Chromium`
- `Mozilla`
- `WebKit`

These values correspond to `clang-format` code style presets, and are _case-sensitive_.

```yaml
c:
    formatter:
        name: clang-format
        config:
            style: LLVM
```

For the other formatters, you _shouldn't_ fill the `config` field. If you want to fine-tune the output, you can do it via putting configuration files for the respective tools in your repository. _This capability is going to be available soon._

Here's another example:
```yaml
rust:
    formatter:
        name: rustfmt
        ignore_paths:
            - samples/ 
```

### Configuring checkers
While only one formatter is available for every language at a time, you can have as many checkers as you want, and configure multi-lang individually for every language. Here's how one entry of a checkers list is structured:
- `name` of the checker. This field is __mandatory__
- `ignore_paths`, discussed in detail above — optional
- `config` — some checkers allow for configuration shortcuts. This field is required for some checkers, optional for some more, and for others, it is an error to include it.

As you can see, the structure is just the same as it is for formatters.

Here are checkers available now:
- `long_method`, detecting functions and methods longer than _X_ lines, available for C, C++, Python and JS
- `high_cyclomatic_complexity`, detecting functions and methods with cyclomatic complexity above _X_, available for C, C++, Python and JS
- `long_parameter_list`, detecting functions and methods with more than _X_ parameters, available for C, C++, Python and JS
- `long_variable_name`, detecting variables and functions with names longer than _X_ symbols, available for Python and JS
- `clang-tidy`, available for C and C++

While `clang-tidy` does not have a respective config entry so far, the other checkers do, to represent the aforementioned _X_. In all cases, there will be the single `threshold` field, which should be a positive integer:

```yaml
c:
    checkers:
        - name: clang-tidy
        - name: high_cyclomatic_complexity
          config:
              threshold: 35
```
