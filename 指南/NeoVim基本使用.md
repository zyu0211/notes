---
title: vim 和 neovim 的基本使用
description: 记录我的 vim 的基础设置，以及使用 lazyvim 的常用快捷键
tags:
  - vim
  - neovim
Author: Yuzh
data: 2023-10-16
---

## 1、快捷键

- default `<leader>` is `<space>`
- default `<localleader>` is `\`

### 1.1、自定义设置

|快捷键设置|使用说明|模式|
|---|---|---|
| `ij` | 映射到 `<Esc>` 键，退出 insert 模式 | normal |
| `v` | 映射到 `<Esc>` 键，推出 visual 模式 | visual |
| `H` | 映射到 `^` | normal |
| `L` | 映射到 `$` | normal |
| `<leader>w` | 映射到 `:w!<Cr>` | normal |
| `<leader>q` | 映射到 `:q!<Cr>` | normal |

### 1.2、基本使用

_窗口管理_

|快捷键设置|使用说明|模式|
|---|---|---|
|`<C-h>`|Go to left window|**n**, **t**|
|`<C-j>`|Go to lower window|**n**, **t**|
|`<C-k>`|Go to upper window|**n**, **t**|
|`<C-l>`|Go to right window|**n**, **t**|
|`<C-Up>`|Increase window height|**n**|
|`<C-Down>`|Decrease window height|**n**|
|`<C-Left>`|Decrease window width|**n**|
|`<C-Right>`|Increase window width|**n**|
|`<leader>wd`|Delete window|**n**|
|`<leader>w-`|Split window below|**n**|
|`<leader>w\|`|Split window right|**n**|
|`<leader>-`|Split window below|**n**|
|`<leader>\|`|Split window right|**n**|

_拖拽行_

|快捷键设置|使用说明|模式|
|---|---|---|
|`<A-j>`|Move down|**n**, **i**, **v**|
|`<A-k>`|Move up|**n**, **i**, **v**|

_查找_

|快捷键设置|使用说明|模式|
|---|---|---|
|`n`|Next search result|**n**, **x**, **o**|
|`N`|Prev search result|**n**, **x**, **o**|

_文件操作_

|快捷键设置|使用说明|模式|
|---|---|---|
|`<leader>fn`|New File|**n**|
|`<leader>cf`|Format|**n**, **v**|
|`<C-s>`|Save file|**i**, **x**, **n**, **s**|
|`<leader>qq`|Quit all|**n**|

_终端_

|快捷键设置|使用说明|模式|
|---|---|---|
|`<leader>ft`|Terminal (root dir)|**n**|
|`<leader>fT`|Terminal (cwd)|**n**|
|`<c-/>`|Terminal (root dir)|**n**|

_lazyvim相关_

|快捷键设置|使用说明|模式|
|---|---|---|
|`<leader>L`|LazyVim Changelog|**n**|
|`<leader>l`|Lazy|**n**|

_bufferLine_

|快捷键设置|使用说明|模式|
|---|---|---|
|`[b`|Prev buffer|**n**|
|`]b`|Next buffer|**n**|

_neo-tree_

|快捷键设置|使用说明|模式|
|---|---|---|
|`<leader>e`|Explorer NeoTree (root dir)|**n**|
|`<leader>E`|Explorer NeoTree (cwd)|**n**|
|`<leader>ge`|Git explorer|**n**|
|`<leader>be`|Buffer explorer|**n**|