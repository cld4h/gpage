---
title: vim 配置
date: 2019-12-03
---

## vim 插件管理器: vim-plug

如下配置将在没有 `vim-plug` 时自动下载该插件（即 `plug.vim` 文件）

```
" ===
" === Auto load vim-plug for first time uses
" ===
if ! filereadable(expand('~/.config/nvim/autoload/plug.vim'))
        echo "Downloading junegunn/vim-plug to manage plugins..."
        silent !mkdir -p ~/.config/nvim/autoload/
        silent !curl "https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim" > ~/.config/nvim/autoload/plug.vim
endif
```

基本上做的事情就是创建了autoload文件夹后下载了`plug.vim`文件。

安装的插件会存放在 `plugged` 目录下


## 杂项

### 输入当前时间信息

```
a<C-R>=strftime("%c")<CR><Esc>
```

应用如将 `F2` 键映射为输入hexo文档信息

```
nmap <F2> a---<CR>title: <++><CR>date: <C-R>=strftime("%Y-%m-%d")<CR><CR>---<CR><Esc>
```

其效果如下:

```md
---
title: <++>
date: 2019-12-04
---
```
