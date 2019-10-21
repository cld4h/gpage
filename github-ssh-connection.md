---
title: 通过SSH连接github，配置ssh-agent避免频繁输入用户名密码
date: 2019-09-30 14:23:24
tags:
---

参考:

https://stackoverflow.com/posts/38980986/revisions
https://help.github.com/en/github/using-git/which-remote-url-should-i-use
https://developer.github.com/v3/guides/using-ssh-agent-forwarding/

### 添加用户的systemd服务

在 `~/.config/systemd/user/ssh-agent.service` 中添加如下内容：
```
[Unit]
Description=SSH key agent

[Service]
Type=forking
Environment=SSH_AUTH_SOCK=%t/ssh-agent.socket
ExecStart=/usr/bin/ssh-agent -a $SSH_AUTH_SOCK

[Install]
WantedBy=default.target
```

### 在 ssh 的 config 文件中添加

`ForwardAgent yes`

对于SSH 7.2版本以后支持在配置文件设置自动将秘钥加入ssh-agent，无须向下面一样通过 `ssh-add` 加入秘钥。配置的过程十分简单：只需要在 ssh 的 config 文件中加入 `AddKeysToAgent yes` 即可。

### 在 `.profile` 文件中添加如下内容：

```.profile
export SSH_AUTH_SOCK="$XDG_RUNTIME_DIR/ssh-agent.socket"
ssh-add /home/hao/.ssh/github_key >/dev/null
```

### 测试连接

`ssh -T git@github.com`
