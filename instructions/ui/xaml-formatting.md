# XAML / AXAML Formatting

Optional module for WPF and Avalonia projects. Skip this file for non-UI repositories.

## Layout

1. The first property of a control stays on the same line as the opening tag:

```xml
<Button Command="{Binding <Command>}"
```

2. Each subsequent property goes on a new line, indented to align with the first property.
3. Separate each individual control from the previous one with an empty line.

Exceptions:

- `<Run>` elements inside a `<TextBlock>` stay on one line; whitespace between runs is rendered.
- `RowDefinition` / `ColumnDefinition` declarations are not separated by blank lines.
- `Setter` elements inside one trigger are grouped compactly.

## Colors

Never use 8-digit hex colors such as `#rrggbbaa`. Use an explicit brush with a separate opacity:

```xml
<Setter Property="Background">
    <Setter.Value>
        <SolidColorBrush Color="#48bbff" Opacity="0.12"/>
    </Setter.Value>
</Setter>
```

Avoid:

```xml
<Setter Property="Background" Value="#48bbff1f"/>
```

For solid colors without transparency, `Value="#RRGGBB"` is sufficient.

Hex-alpha to opacity mapping:

| Hex | Opacity |
|---|---|
| `1a` | 0.10 |
| `1f` | 0.12 |
| `33` | 0.20 |
| `3d` | 0.24 |
| `4d` | 0.30 |
| `66` | 0.40 |
| `80` | 0.50 |

## Styles

- Use named styles from shared resource dictionaries; avoid inline styles.
- Use `BasedOn` to inherit shared properties such as `CornerRadius`, `Padding`, `FontSize`, and `FontWeight`.
- For enum-driven appearance, use a converter that resolves a resource key instead of inline `Style.Triggers`.

```xml
<Border Style="{Binding Status, Converter={StaticResource <Status>Converter}, ConverterParameter=Badge}"/>
```

Define style families in `Resources/<Entity>Styles.xaml` and merge them into `App.xaml`.

## Avalonia differences

- Avalonia uses `ControlTheme` instead of `Style` for theming.
- Use attached `TextBlock.Foreground` inside the same theme instead of a separate `TextBlock` style.
- One `ControlTheme` represents one logical unit: container plus text.
- Opacity values use a leading dot: `.12`, `.24`, not `0.12`, `0.24`.
- A comment describing the color is required before every `Setter`.
- `BasedOn` is mandatory for shared properties.

```xml
<ControlTheme x:Key="<Entity>Theme"
              TargetType="Border"
              BasedOn="{StaticResource Base<Entity>Theme}">
    <!-- Dark gray background -->
    <Setter Property="Background">
        <Setter.Value>
            <SolidColorBrush Color="#E0E0E0" Opacity=".12"/>
        </Setter.Value>
    </Setter>
</ControlTheme>
```

Before editing an existing `.axaml` view, open a representative file and the platform bootstrapper. Do not patch Avalonia views "by analogy" without verifying the project's actual namespaces and control names.
