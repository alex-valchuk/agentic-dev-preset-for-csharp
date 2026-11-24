---
name: add-localization-key
description: Add a new localization key to Lang.resx, all Lang.<culture>.resx, and Lang.Designer.cs. Use when adding a new UI string, label, tooltip, or message that must be localized.
---

# Add Localization Key

## When to use

When adding a new user-visible string (button label, tooltip, message, header) that must be localized.

## Where keys live

Locate the resources project in the consuming repository first; do not assume a fixed path.

- `<ResourcesProject>/Localization/Lang.resx` - neutral language (usually English), source of truth
- `<ResourcesProject>/Localization/Lang.<culture>.resx` - translations (ru, de, ...)
- `<ResourcesProject>/Localization/Lang.Designer.cs` - strongly typed properties

## Key naming convention

PascalCase with a module prefix: `<Module>_<Name>`, for example `Settings_Apply`, `Status_Ready`.

## Steps (do NOT edit manually - use a script)

Manual editing of all three files is error-prone (N keys x 3 files = 3N edits). Use a PS1 script
named `add-<feature>-loc.ps1` that is idempotent and does:

1. `Lang.resx` - add `<data name="<Key>" xml:space="preserve"><value>...</value></data>`.
2. `Lang.<culture>.resx` - same for each discovered locale (regex `^Lang\.[a-zA-Z]{2}(-[a-zA-Z]{2,4})?\.resx$`).
3. `Lang.Designer.cs` - add `public static string <Key> { get { return ResourceManager.GetString("<Key>", resourceCulture); } }`
   with `/// <summary>Looks up a localized string...</summary>` above.

## Rules

- BOM and line endings of each file MUST be preserved (detect by first 3 bytes + `Contains("\r\n")`).
- Default - apply, not dry-run. `-DryRun` - optional preview, writes nothing.

## Verification

After running the script:

1. `dotnet build <SolutionFile>.sln` - must succeed (Designer must see new properties).
2. Open the app, navigate to the affected view - the new string must render.
3. Switch culture in app settings - the translation must appear.

## References

- `instructions/localization.md` - full details on .resx XML, Lang.Designer, edge cases.
- `instructions/powershell.md` - PowerShell scripting notes.
