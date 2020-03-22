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

## other

通过ffmpeg批量转换视频封装格式：mkv to mp4
[参考](https://gist.github.com/jamesmacwhite/58aebfe4a82bb8d645a797a1ba975132)

```sh
for f in *.mkv; do ffmpeg -i "$f" -c copy "${f%.mkv}.mp4"; done
```

在 windows 下

```bat
for /R %f IN (*.mkv) DO ffmpeg -i "%f" -c copy "%~nf.mp4"
```
