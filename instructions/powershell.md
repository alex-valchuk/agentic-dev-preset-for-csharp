# PowerShell Scripting Notes

Only relevant when an agent actually creates a `.ps1` file. Scripts are written directly into the repository and run from its root.

## Syntax pitfalls (Windows PowerShell 5.1)

- Always use curly braces with `if`, even for one line: `if (-not $x.Contains($anchor)) { throw "..." }`. Omitting them can cause `MissingStatementBlock`.
- With interpolation like `"${var}:${var2}"`, the braces after `$var` are required before the colon; otherwise you get `InvalidVariableReferenceWithDrive`.
- A here-string (`@'...'@`) must start at the very beginning of a line - no indentation.
- `[System.IO.File]::WriteAllText($path, $content)` writes UTF-8 **without** BOM.
- Detect line endings with `$raw.Contains("`r`n")`: true means CRLF, otherwise LF. Preserve the original style.
- Do not use `List<T>` when patching `.sln` files. In PS 5.1 those objects are wrapped in `PSObject`, which breaks `.Insert` and `.IndexOf`. Use `String.Replace`.

## Safe patching

- Ensure idempotency: check `$x.Contains($new)` before `$x.Replace($old, $new)`.
- When patching "between X and Y", use an explicit anchor and `throw` if it is not found.
- For multi-file operations, do all creation steps first, then all patching steps with verification.
- Prefer a few explicit anchors over broad regex substitution.
