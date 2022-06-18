# neodim
Neovim plugin for dimming the highlights of unused functions, variables, parameters, and more

This plugin takes heavy inspiration from https://github.com/NarutoXY/dim.lua. The implementation in NarutoXY/dim.lua was a bit inefficient and I saw room for various improvements, including making the dimming an actual LSP handler, rather than an autocmd. The result is a much more polished experience with greater efficiency.

The plugin is finally out of early alpha, and I appear to have it stable. Getting things to not be dimmed on pressing 'jk' for escape was quite the adventure, but it finally works without any autocmd hacks that cause some LSPs issues! As far as I can tell it is perfectly stable.

### Setup:

Install the plugin like any other:

```
use {
  "zbirenbaum/neodim",
  event = "LspAttach",
  config = function ()
    require("neodim").setup({
      blend_color = "#000000"
      alpha = 0.75
      hide = {
        virtual_text = true,
        signs = true,
        underline = true,
      }
    })
  end
}
```

### Options:

#### Decoration Options
All decorations can be hidden for diagnostics pertaining to unused tokens. By default, hiding all of them is enabled, but you can re-enable them by changing the config table passed to neodim. It is important to note that regardless of what you put in this configuration, neodim will always respect settings created with `vim.diagnostic.config`. For example, if all underline decorations are disabled by running `vim.diagnostic.config({ underline=false })`, neodim will ***not*** re-enable them for "unused" diagnostics.

Example:

```
-- re-enable only sign decorations for 'unused' diagnostics
require("neodim").setup({
  hide = {signs = false }
})
```

```
-- renable all decorations for 'unused' diagnostics
require("neodim").setup({
  hide = {
    virtual_text = false,
    signs = false,
    underline = false,
  }
})
```

#### Dim Highlight Options

##### blend_color

`blend_color` controls the color which is used to dim your highlight. Black is the default, but you could set this to your terminal or neovim background color to make it more seamless.

Example:

```
require("neodim").setup({
  blend_color = "#10171f"
})
```

##### alpha

Alpha controls how dim the highlight becomes. A value of 1 means that dimming will do nothing at all, while a value of 0 will make it identical to #000000 or the color set in `blend_color`. Conceptually, if you were to place the text to be dimmed on a background of `blend_color`, and then set the opacity of the text to the value of alpha, you would have the resulting color that the plugin highlights with.


```
require("neodim").setup({
  alpha = 0.5 -- make the dimmed text even dimmer
})
```

### How to get live dim updates as you type

The vim.diagnostic.config function provides hooks which allow you to affect the behavior of this plugin. Setting `update_in_insert` to true will cause the plugin to update as fast as your LSP can supply diagnostic info. I personally find it preferable to keep this value at false, but the option is there and I recommend trying both out to see which you prefer.

Example:
```
vim.diagnostic.config({
  ...
  update_in_insert = true, -- Set this to true for live dim updates as you type
  ...
})
```
