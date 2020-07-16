#### rust-clippy

A collection of lints to catch common mistakes and improve your Rust code.

Clippy compiles project and reports any found issues. If compilation fails, it will report occurred errors in a review.
In order for Clippy to work repository should contain file `cargo.toml`.

[**project page**](https://github.com/rust-lang/rust-clippy)

**Name in config:** `rust-clippy`

**Supported languages:** `Rust`

**Requires `project_roots`:** `Yes`

**Available configuration parameters**

|  **Name**  | **Description** |                                                                                        **Possible values**                                                                                         | **Default value** | **Mandatory** |
| :--------: | :-------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :---------------: | :-----------: |
| **output** | Type of report. | <ul><li>`pass-fail` — post reports form clippy  as dedicated review comments, tied to specific strings.</li> <li>`all` — post all reports from clippy as a single comment in review body.</li><ul> |       **—**       |      No       |