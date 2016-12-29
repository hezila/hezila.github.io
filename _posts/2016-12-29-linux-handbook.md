---
layout: post
title: Linux命令手册
tags: [Linux, Command]
keywords: [linux, 命令]
---


### `Find` 命令

```
# Find would only return the files that were named .png,
# Not files that contained in their name the string .png
$ find -name .png
.png

# Fortunately, You can use shell wild cards with find, But must be quoted
$ find -name "*.png"
Mahavir.png	Ganesha.png	Hello.png	.png

# Search for files named snow.png in the current working directory
$ find -name snow.png

# Serach for files named snow.png Snow.png SNOW.PNG etc in the CWD
$ find -iname snow.png

# Search files anywhere on the system that ends with .txt
$ find / -name "*.txt"

# Search files in the /etc directory that contain pass in their names
$ find /etc -name "*pass*"

# Search files owned by the user joe and the group joe in the /home directory
$ find /home -user joe -group joe

# Search files owned by UID 500 and the GID 600 in the /home directory
$ find /home -uid 500 -gid 600

```

### 装逼用的

- 使用 `ps & grep` 命令搜索进程时，结果中总是包含grep本身这条记录, 例如 `ps -aux | grep skype`, 如果使用下面的命令

    ps -aux | grep [s]skype

结果中就不包含grep行。

- 使用 `[Ctrl] + [R]` 重复执行以前的命令
