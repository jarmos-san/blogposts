---
title: "How to Create Custom Keymaps in Neovim v0.12 With Lua"
description:
  Deep dive into Neovim keymap configuration using Lua. Explore
  vim.keymap.set(), mode handling, and extensible patterns for building
  maintainable and ergonomic editor workflows.
publishedOn: 2021-11-10
status: published
coverImage:
  url: https://ik.imagekit.io/jarmos/creating-neovim-keymaps-using-lua.png?updatedAt=1702974989266
  alt: How to create custom keymaps in Neovim with Lua?
sitemap:
  loc: /create-custom-neovim-keybindings-using-lua
  lastmod: 2026-05-03
  changefreq: yearly
  priority: 1
---

**NOTE**: The article was recently updated to reflect the changes introduced in
the latest version of Neovim (currently v0.12+).

[Neovim](https://neovim.io) is a modern, extensible, and highly customizable
text editor built as a fork of [Vim](https://www.vim.org). One of its major
advantages over alternatives is the built-in [Lua](https://www.lua.org) runtime.
In addition to Lua's standard library, Neovim exposes an "editor libraryj"
through the global `vim` namespace and a Lua-based API, enabling deep and
flexible customization.

A comprehensive overview of everything Neovim can do is beyond the scope of this
article. Instead, this guide focuses on a practical starting point: setting up
custom keymaps using Lua.

## A Brief Introduction to Lua

If you're unfamiliar with Lua and its nuances, Neovim provides comprehensive
documentation for both the language and its integration. You can explore these
in [`:h lua`](https://neo.vimhelp.org/lua.txt.html) and
[`:h luaref`](https://neo.vimhelp.org/luaref.txt.html).

When setting up your own keymaps, you'll also want to refer to
[`:h vim.keymap`](https://neo.vimhelp.org/lua.txt.html#vim.keymap).

Keymaps can be defined in `~/.config/nvim/init.lua`,
`~/.config/nvim/lua/keymaps.lua`, or any other module, as long as it resides
within Neovim's runtime path (see
[`:h 'runtimepath'`](https://neo.vimhelp.org/options.txt.html#%27runtimepath%27)
for details).

For brevity, this section stays focused on keymaps. If you'd like a deeper
understanding of Lua, here are a few curated resources:

- [Everything You Need to Start Writing Lua by TJ DeVries](https://youtu.be/CuWfgiwI73Q)
- [Learn Lua in Y Minutes](https://learnxinyminutes.com/lua)

## Managing the Lua-based Keymaps

Now that you're familiar with Lua, you can define keymaps using
[`:h vim.keymap.set()`](https://neo.vimhelp.org/lua.txt.html#vim.keymap.set%28%29).
This function accepts four parameters:

**Note**: It's worth reviewing
[`:h :map-modes`](https://neo.vimhelp.org/map.txt.html#%3Amap-modes) to better
understand how Neovim handles different mapping modes.

- `{modes}`: A string or list of strings representing Vim modes (see
  [`:h vim-modes`](https://neo.vimhelp.org/intro.txt.html#vim-modes)).
- `{lhs}`: The left-hand side of the mapping (the key sequence you press).
- `{rhs}`: The right-hand side of the mapping, either a Lua string or a function
  executed when the mapping is triggered.
- `{opts}`: A table of options (see
  [`:h map-arguments`](https://neo.vimhelp.org/map.txt.html#%3Amap-arguments)).

Here are a few examples:

```lua
local map = vim.keymap.set

map('i', 'jk', '<ESC>', { desc = 'Change to NORMAL mode' })
map('n', 'H', '<HOME>', { desc = 'Move to the beginning of the line' })
map('n', 'L', '<END>', { desc = 'Move to the end of the line' })

-- Call a function when a keymap is invoked
map('n', 'gD', vim.lsp.buf.declaration, { desc = 'Jump to the object declaration' })
```

For brevity, these examples only cover a small subset of what's possible. You're
encouraged to build on them and define mappings that suit your workflow-how you
personalize your Neovim setup is entirely up to your needs and preferences.

## Parting Words

As demonstrated, building a personalized Neovim experience is straightforward,
largely due to its embedded Lua runtime. Compared to Vimscript, Lua provides a
more expressive and ergonomic interface for defining keymaps, making
configuration both intuitive and maintainable.

This article only scratches the surface of what's possible. If you're looking to
extend your setup further or explore real-world patterns, take a look at my
[dotfiles](https://github.com/Jarmos-san/dotfiles) repository-specifically the
[Neovim keymaps](https://github.com/Jarmos-san/dotfiles/tree/main/dotfiles/.config/nvim/lua/keymapshtt).
Use it as a reference, adapt what fits your workflow, and iterate on it to suit
your own editing model.
