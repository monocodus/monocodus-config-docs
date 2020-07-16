#### Clang-format

A tool to format C/C++/Java/JavaScript/Objective-C/Protobuf/C# code.

[**project page**](https://clang.llvm.org/docs/ClangFormat.html)

Currently it is used by monocodus only for C/C++ code, another languages will be presented soon.


**Name in config:** `clang-format`

**Supported languages:** `C`, `C++`

**Available configuration parameters**

| **Name**  |                         **Description**                         |                **Possible values**                | **Default value** | **Mandatory** |
| :-------: | :-------------------------------------------------------------: | :-----------------------------------------------: | :---------------: | :-----------: |
| **style** | Use one of predefined coding styles. Options are case-sensitive | `LLVM`, `Google`, `Chromium`, `Mozilla`, `WebKit` |       **—**       |      No       |