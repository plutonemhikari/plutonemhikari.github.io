---
title: proxychains自动补全
tags: Linux
categories: Tutorial
abbrlink: 2241119764
date: 2021-02-25 11:49:43
---

proxychains是Linux下一款很好用的终端代理神器，命令行运行的程序如需要代理，最便捷的方法之一就是使用proxychains。但是proxychains有一个缺点，默认状态下proxychains后面的命令无法自动补全。有的教程是利用`complete -c proxychains`，但这样实际上达不到补全的效果，它只能补全一层命令，也就是说，`proxychains`后面的第一个命令的名称是可以补全的，但这个命令后面接的子命令或参数就无法补全了，所以我们需要自行编写补全脚本。<!--more-->

# 脚本

proxychains的补全规则和sudo是类似的，我们可以参照sudo的补全脚本来写proxychains的补全脚本，写出补全脚本的代码如下：

```shell
# bash completion for proxychains4(1)                              -*- shell-script -*-

_proxychains()
{
    local cur prev words cword split
    _init_completion -s || return

    local i

    for (( i=1; i <= cword; i++ )); do
        if [[ ${words[i]} != -* ]]; then
            local PATH=$PATH:/sbin:/usr/sbin:/usr/local/sbin
            local root_command=${words[i]}
            _command_offset $i
            return
        fi
        [[ ${words[i]} == \
            -@(user|other-user|group|close-from|prompt|!(-*)[uUgCp]) ]] \
            && ((i++))
    done

    case "$prev" in
        --user|--other-user|-!(-*)[uU])
            COMPREPLY=( $(compgen -u -- "$cur") )
            return
            ;;
        --group|-!(-*)g)
            COMPREPLY=( $(compgen -g -- "$cur") )
            return
            ;;
        --close-from|--prompt|-!(-*)[Cp])
            return
            ;;
    esac

    $split && return

    if [[ "$cur" == -* ]]; then
        local opts=$(_parse_help "$1")
        COMPREPLY=( $(compgen -W '${opts:-$(_parse_usage "$1")}' -- "$cur") )
        [[ $COMPREPLY == *= ]] && compopt -o nospace
        return
    fi
} &&
complete -F _proxychains proxychains

# ex: filetype=sh
```

将其复制并保存为文本，命名为proxychains，或者从[这里](https://raw.githubusercontent.com/plutonemhikari/proxychains-completion/master/proxychains)下载，然后放在`/usr/share/bash-completion/completions/`中，如果你的电脑上补全脚本并不在这个目录，那就放在你电脑上补全脚本的目录里。

# 结果

![](/images/pxychain.png)

可以看到proxychains之后的命令可以真正地自动补全了。
