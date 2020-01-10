---
title: Bash notes
date: 2019-11-27
tags:
---

## Bash prompt

```sh
PS1="$\[(tput setaf 166)\]\u"	#orange user
PS1+="$\[(tput setaf 228)\]@\h"	#yellow host
PS1+="$\[(tput setaf 71)\]\W -> "
PS1+="$\[(tput sgr0)\]"	#end color setting
```

## man pages

搜索时，输入全部为小写时进行大小写不敏感的搜索。
如：

* `/invoc` : 大小写不敏感
* `/Invoc` : 大小写敏感
* `/INVOC` : 大小写敏感
