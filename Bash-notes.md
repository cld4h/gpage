---
title: hexo 的配置及使用
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

