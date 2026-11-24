# Repository + Service Pattern

Anything written to disk and surviving beyond a single session follows **Repository + Service**. Domain types stay pure.

## Layer layout

| Layer | Assembly | Responsibility |
|---|---|---|
| Domain POCO | `*.DomainModel` | Plain data, no `INotifyPropertyChanged`, no I/O |
| Repository interface | `*.Repositories.Abstract` | `I*Repository` contracts |
| JSON implementation | `*.Repositories.Json` | File-based implementation, base class, path constants |
| Service interface | `*.Services.Abstractions` | `I*Service` contracts |
| Service implementation | `*.Services` | Thin orchestration over repositories |

Keep interfaces and implementations in **separate assemblies**. If a second storage backend is needed later, add a new implementation assembly and change one DI registration; services and consumers remain untouched.

## Repository rules

- Repositories inherit from a shared base such as `JsonRepositoryBase` that provides `TryReadJson<T>`.
- Serializer options are an instance field with `WriteIndented = false`, not a static field.
- Paths are built only through `Environment.SpecialFolder.ApplicationData` plus constants such as `StrConst._xxxFolder`.
- Methods are `CreateAsync`, `ReadAsync`, `ReadByIdAsync`, `UpdateAsync`, `DeleteAsync`, each returning `Task<T>`.
- Use `Task.FromResult` or `Task.CompletedTask` when no actual I/O is performed.
- `UpdateAsync` for a missing entity throws `FileNotFoundException` with a clear message.
- No `CancellationToken`, `SemaphoreSlim`, events, or in-memory caching in repositories unless the project explicitly overrides this rule.

`TryReadJson<T>` returns `bool` plus an `out T` value and never throws: on a corrupted file it returns `false` and `data = null`, so one bad record does not invalidate the whole list.

## File artifacts

Binary artifacts such as thumbnails live as separate files next to the JSON, not as Base64 inside it. Delete and read them alongside the JSON record.

## CSV and other exports

Exports belong in the service layer, not the repository. Use `CultureInfo.InvariantCulture` for numbers, round-trip timestamps with `"O"`, and escape quotes correctly.

## DI registration

Register repositories and services through the container, for example:

```csharp
containerRegistry.RegisterSingleton<IFleetRepository, JsonFleetRepository>();
containerRegistry.RegisterSingleton<IFleetService, FleetService>();
```

Do not manually construct services with paths inside the registration.

## Nullable mode

Many older C# projects run with `<Nullable>disable</Nullable>` or no flag at all. In that mode:

- Reference types are written **without** `?`, even when conceptually optional.
- Value types use `Nullable<T>` / `?` when optional.
- `Task<T?>` is written as `Task<T>`.
- A null cast such as `(Entity?)null` is simply `null`.

```csharp
public string SerialNumber { get; set; }        // correct
public Guid? MissionId { get; set; }            // correct
public string? SerialNumber { get; set; }       // wrong in old-style mode
```
