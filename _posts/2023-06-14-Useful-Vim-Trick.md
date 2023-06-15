---
title: Better vertical navigation in Vim
date: 2023-06-14 00:00:00
categories: [Tools, Vim]
tags: [vim, neovim]
---

# Better vertical navigation in Vim


If you're anything like me, you often find yourself navigating code with Vim. Now one of the motions you'll learn for vertical navigation is `<C-d>` to move down by half a page and `<C-u>` to move up by half a page.

The problem with these motions, out of the box, is that if you're at the top of the page and you `<C-d>` then the visible portion of your file will be moved while your cursor will be kept at the top, causing you to lose sight of the context from the preceding lines of code.

The same can be said about `<C-u>`, if you're at the bottom of the viewport and you use this motion, your cursor will stay at the bottom of your screen, causing you to lose the context of the lines that come after your current line.

This is why I like to remap `<C-d>` and `<C-u>` like this:
```
-- Centering after <C-d> and <C-u>
nnoremap <C-d> <C-d>zz
nnoremap <C-u> <C-u>zz
```
Or for Neovim users:
```lua
-- Centering after <C-d> and <C-u>
vim.api.nvim_set_keymap('n', '<C-d>', '<C-d>zz', {silent = true})
vim.api.nvim_set_keymap('n', '<C-u>', '<C-u>zz', {silent = true})
```
