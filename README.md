# zig-xaml-compiler

XAML compiler for Zig — generates XBF (XAML Binary Format) + PRI (Package Resource Index) from `.xaml` files without MSBuild or Visual Studio.

## Motivation

WinUI3's XAML compiler (`Microsoft.UI.Xaml.Markup.Compiler.dll`) is an MSBuild task that only works with C#/C++ projects. This makes it impossible to use `.xaml` files from Zig, Rust, or any non-MSBuild build system.

This tool fills that gap: parse `.xaml`, emit `.xbf` + `.pri`, generate Zig code-behind.

## Status

**Early research phase.** See [ghostty-win#58](https://github.com/YuujiKamura/ghostty/issues/58) for context.

## Approach

Three approaches under evaluation (in priority order):

### A: XamlBinaryWriter API (preferred)
Call `Microsoft.UI.Xaml.Markup.XamlBinaryWriter.Write()` at build time to generate XBF. Requires bootstrapping a WinRT context, but avoids reimplementing the binary format.

### B: XamlCompiler.exe standalone
Invoke the official XAML compiler directly via JSON I/O (source is open in microsoft-ui-xaml repo).

### C: Full reimplementation
Parse XAML, emit XBF binary, generate PRI — all from scratch using reverse-engineered format specs.

## Reference Code

| Repository | Language | Purpose |
|-----------|----------|---------|
| [microsoft-ui-xaml/XamlCompiler](https://github.com/microsoft/microsoft-ui-xaml/blob/main/src/src/XamlCompiler/BuildTasks/CompileXaml.cs) | C# | Official XAML compiler source |
| [XbfDecompiler](https://github.com/TeamGnome/XbfDecompiler) | C# | XBF format parser (reverse-engineered) |
| [XbfAnalyzer](https://github.com/misenhower/XbfAnalyzer) | C# | XBF v2.1 analyzer |
| [PriTools](https://github.com/chausner/PriTools) | C# | PRI format reader + unofficial spec |
| [WindowsAppSDK/MRTCore](https://github.com/microsoft/WindowsAppSDK/tree/main/dev/MRTCore/mrt/mrm) | C++ | PRI generation (open source) |
| [XamlX](https://github.com/kekekeks/XamlX) | C# | Pluggable XAML compiler (Avalonia) |

## Related Projects

- [win-zig-bindgen](https://github.com/YuujiKamura/win-zig-bindgen) — WinMD → Zig COM vtable generator (same approach, different target)
- [ghostty-win](https://github.com/YuujiKamura/ghostty) — WinUI3 terminal emulator that needs this tool

## License

MIT
