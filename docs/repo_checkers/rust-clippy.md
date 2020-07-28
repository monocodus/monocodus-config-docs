#### rust-clippy

A collection of lints to catch common mistakes and improve your Rust code.

Clippy compiles project and reports any found issues. If compilation fails, it will report occurred errors in a review.
In order for Clippy to work repository should contain file `cargo.toml`.

[**project page**](https://github.com/rust-lang/rust-clippy)

**Name in config:** `rust-clippy`

**Supported languages:** `Rust`

**Requires `project_roots`:** `Yes`

**Available configuration parameters**

|  **Name**  | **Description** |                                                                                  **Possible values**                                                                                  | **Default value** | **Mandatory** |
| :--------: | :-------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :---------------: | :-----------: |
| **output** | Type of report. | <ul><li>`pass-fail` — report only short summary of issues found by clippy.</li> <li>`all` — post each clippy warning and error as dedicated comment tied to specific string.</li><ul> |       `all`       |      No       |