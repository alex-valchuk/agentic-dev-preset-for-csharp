# Localization (.resx + Designer)

## Files

In the localization folder of the resources project:

- `Lang.resx` — neutral language (usually English), the source of truth.
- `Lang.<culture>.resx` — translations, for example `Lang.ru.resx`, `Lang.de.resx`, `Lang.ru-RU.resx`.
- `Lang.Designer.cs` — strongly typed properties used by code and markup.

## Conventions

- Keys are PascalCase with a module prefix: `<Module>_<Name>`.
- A neutral key must exist; otherwise `{x:Static rs:Lang.<Key>}` in XAML fails to build.
- For formatted strings, use placeholders `{0}`, `{1}` and call `string.Format(Lang.Key, ...)`.
- In XAML, import the generated class and bind statically:

```xml
xmlns:rs="clr-namespace:<ResourcesNamespace>;assembly=<ResourcesAssembly>"
Text="{x:Static rs:Lang.<Key>}"
```

## Adding keys: use a script, not manual edits

Adding N keys touches 3N files. Use an idempotent script named `add-<feature>-loc.ps1` that:

1. Adds `<data name="<Key>" xml:space="preserve"><value>...</value></data>` to `Lang.resx`.
2. Adds the same entry to every discovered `Lang.<culture>.resx`. Use a regex such as `^Lang\.[a-zA-Z]{2}(-[a-zA-Z]{2,4})?\.resx$`.
3. Adds a property to `Lang.Designer.cs`:

```csharp
/// <summary>Looks up a localized string similar to <Key>.</summary>
public static string <Key> {
    get { return ResourceManager.GetString("<Key>", resourceCulture); }
}
```

Script rules:

- Preserve BOM and line endings of each file.
- Default to applying changes; `-DryRun` optionally previews without writing.

## .resx XML details

- `xml:space="preserve"` is mandatory for strings with leading or trailing whitespace.
- Escape values: `&` → `&amp;`, `<` → `&lt;`, `'` → `&apos;`.
- Entry order does not matter, but keep alphabetical or logical grouping.

## Designer file

Avoid hand-editing when Visual Studio can regenerate it. When adding keys via script, the Designer file must be updated manually or the build will not see the new properties.

## Verification

1. `dotnet build <SolutionFile>.sln`.
2. Open the affected view and confirm the string renders.
3. Switch the UI culture and confirm the translation appears.
