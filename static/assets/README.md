# Custom Theme Assets

## Solarized Dark Color Scheme

This directory contains a custom `solarized-dark.css` file that implements the Solarized Dark color palette for the Hugo Terminal theme.

### Colors Used

Based on the official Solarized Dark palette:
- **Background**: `#002b36` (base03)
- **Text**: `#839496` (base0)
- **Accent**: `#2aa198` (cyan)
- **Border**: `rgba(7, 54, 66, 0.5)` (base02 with transparency)
- **Emphasized**: `#93a1a1` (base1)

### Usage

To activate the Solarized Dark theme, set the following in `config.toml`:

```toml
[params]
  themeColor = "solarized-dark"
```

### Maintenance

This CSS file overrides the Terminal theme's color scheme without modifying the theme submodule. It was created by copying `themes/terminal/static/assets/blue.css` and replacing the color values with Solarized Dark equivalents.

To update if the base theme changes:
1. Copy the updated `themes/terminal/static/assets/blue.css`
2. Replace color values using these mappings:
   - `#23b0ff` → `#2aa198` (accent color)
   - `rgb(29, 33, 44)` → `#002b36` (background)
   - `#fff` → `#839496` (text color)
   - `rgb(141, 155, 163)` → `#93a1a1` (emphasized text)
   - Border transparency values as needed

### Credits

- Solarized color scheme by Ethan Schoonover
- Terminal theme by Panr (https://github.com/panr/hugo-theme-terminal)
