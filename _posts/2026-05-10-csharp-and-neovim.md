---
layout: post
author: Shane Skiles
title: "C# and Neovim"
tags: [c#, csharp, neovim, nvim, vim, vi, dotnet, lsp, language server protocol]
---

I write a lot about .NET, Linux, and .NET on Linux.
I'm sure most of you know (or may not know) that VS Code is cross platform and runs on Linux.
This is a wonderful way to work on a variety of projects in almost any language you can imagine.
VS Code allows you to edit and compile your code all in one place.
But, VS Code is not the only way to edit and compile your C# code.

One of the most popular editors on Linux is Neovim.
Neovim is a terminal based text editor that has all of your expected text editor functions along with macros and LSP server support.
The LSP server support is what we are going to go over now.
Language Server Protocol (LSP) support provides "smart" features to code editors.
It enables features like code completion, definitions, documentation, and basic syntax checking.
All of those goodies don't come standard, out of the box - we need to set them up.

Neovim, is an incredibly capable and extensible editor/tool.
It comes with Lua as a built-in scripting language, and hundreds if not thousands of plugins.
I will not go into much detail here, but there are "distros" ar "frameworks" that give basic Neovim a nice IDE like configuration with a variety of plugins.
One such distro is `NvChad`, which is relatively minimalist, but offers a good selection of plugins.
One of those plugins is called `Mason`

We can use this to install C# support in Neovim. 
Here's a quick walk through.

After installing `NvChad`, or a similar package containing `Mason` start Neovim.
- In `Normal` mode, type `:Mason`.
- Navigate down to `csharp-language-server`. Hitting `Enter` will display 'Roslyn-based LSP language server for C#.'
- Type `i` and 'csharp-language-server was successfully installed' should be displayed.
- Edit `~/.config/nvim/lua/configs/lspconfig.lua` and add `csharp_ls` to the line containing the local servers. The line should end up looking something like 
```
local servers = { "html", "cssls", "csharp_ls" }
```
- From there, editing `.cs` files that are part of or in directories that have a `.csproj` or `.sln` will have autocomplete and other LSP goodies.

The part for setting the local servers is something most guides will point you toward `init.lua` to make changes in. 
That may have been the case for older version of Neovim or Mason, but as of Spring 2026, this is the way to do it.
From there, you are on your own to figure out the tips, tricks and shortcuts of Neovim.

For the final part of the development process, compiling and running code, we can just use the `dotnet` command. 
If you are not familiar with the `dotnet` command, you can use it to 'run', 'build' and manage solutions. 
I highly recommend reading up on the documentation and at least familiarizing yourself with some of the capabilities. 
Anything that can be done through a UI can be done through the `dotnet` command.
In some cases, it is simpler and quicker to do it from the command line instead of a UI.
And, if it can be done through the command line, it can be scripted out for simple/easy replication/repetition.

Anyway, now you know how to set up Neovim with a C# LSP for C# development.
Now you can (somewhat) easily edit, compile, and run C# programs from a Linux terminal.
This is a _very_ lightweight way to develop C# as far as resources go.
So, now if you are ever trapped on a Linux machine without a GUI, and you need to knock out a C# program, you know what to do :)

Have fun, `:q!`

Shane

P.S.
Add this to the `init.lua` to resume editting on the same line after quitting if Neovim doesn't do it by default:
```lua
vim.api.nvim_create_autocmd("BufReadPost", {
  callback = function()
    local mark = vim.api.nvim_buf_get_mark(0, '"')
    local line = mark[1]
    if line > 1 and line <= vim.api.nvim_buf_line_count(0) then
      vim.api.nvim_win_set_cursor(0, mark)
    end
  end,
})
```
