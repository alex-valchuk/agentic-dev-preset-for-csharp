# Prism Navigation and UI Thread Marshaling

Optional module for Prism-based WPF/Avalonia projects.

## View registration

Navigation setup typically touches three files:

1. `MainWindow.xaml` — add a navigation button.
2. `MainWindow.xaml.cs` — register the view with a region.
3. `Bootstrapper.cs` — bind the view to its view model.

```csharp
regionManager.RegisterViewWithRegion<<RegionName>, <Xxx>View>();   // region registration
ViewModelLocationProvider.Register<<Xxx>View, <Xxx>ViewModel>();  // explicit VM binding
```

Prefer explicit `ViewModelLocationProvider.Register` calls if that is the project's convention. Do not switch to `prism:ViewModelLocator.AutoWireViewModel="True"` or `RegisterForNavigation<>` just because another tutorial uses them.

A navigation button usually binds like this:

```xml
<Button Command="{Binding NavigateCommand}"
        CommandParameter="<Xxx>View"
        IsEnabled="{Binding Is<Xxx>ViewActive, Converter={StaticResource InvertBooleanConverter}}"/>
```

`CommandParameter` is the view name as a string, not the type. The enabled state is inverted so the button is disabled while that view is active.

## Dialogs

Dialogs are not pages:

```csharp
containerRegistry.RegisterDialog<<Xxx>View, <Xxx>ViewModel>();
```

Open them through `IDialogService.ShowDialog(...)`.

## UI thread marshaling

An `ObservableCollection<T>` bound in XAML can only be modified on the UI thread. Events from timers, telemetry callbacks, thread-pool work, or unawaited `Task.Run` continuations are common offenders.

Bridge them with an `IProgress<T>` created in the view model constructor:

```csharp
public class <Xxx>ViewModelBase : BindableBase
{
    private readonly IProgress<Foo> _onFooUI;

    public <Xxx>ViewModelBase(IFooSource source)
    {
        // The constructor runs on the UI thread, so Progress<T> captures its context.
        _onFooUI = new Progress<Foo>(HandleFooOnUI);
        source.FooChanged += (_, e) => _onFooUI.Report(e.Payload);
    }

    private void HandleFooOnUI(Foo value)
    {
        Items.Clear();
        foreach (var x in value) Items.Add(x);
    }
}
```
