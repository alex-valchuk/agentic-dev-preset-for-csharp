# Solutions and Build Files

## Structural changes

Repositories often keep more than one `.sln` (for example a full solution, a platform-specific solution, and an installer solution). Any structural change — adding a project, adding a reference, changing a target framework — must be applied to **every** solution file that is meant to stay in sync.

Never update only the solution you happen to build locally.

## SDK-style projects without `<Platforms>`

A common MSBuild failure comes from mapping an SDK-style project 1:1 to every solution platform even though the project itself declares no `<Platforms>`.

For such projects, map **all** solution platforms to `Any CPU`:

```text
{ProjectGUID}.Debug|x64.ActiveCfg = Debug|Any CPU
{ProjectGUID}.Debug|x64.Build.0   = Debug|Any CPU
{ProjectGUID}.Debug|ARM64.ActiveCfg = Debug|Any CPU
{ProjectGUID}.Debug|ARM64.Build.0   = Debug|Any CPU
{ProjectGUID}.Release|x64.ActiveCfg = Release|Any CPU
{ProjectGUID}.Release|x64.Build.0   = Release|Any CPU
{ProjectGUID}.Release|ARM64.ActiveCfg = Release|Any CPU
{ProjectGUID}.Release|ARM64.Build.0   = Release|Any CPU
```

Why this matters: a 1:1 mapping such as `x64 → x64` makes MSBuild look for outputs in `obj\x64\Debug\...` while the SDK-style project actually built into `obj\Debug\...`. The result is a `metadata file not found` error.

## Project GUID registry

If the repository maintains a GUID registry for solutions or solution folders, update it in the same change as the `.sln` edits. Keep GUIDs identical across all solutions.

## Verification

After any solution change:

```bash
dotnet build <SolutionFile>.sln
```

Repeat for every solution that was touched.
