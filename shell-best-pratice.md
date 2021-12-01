---
title: SHELL最佳实践
date: 2019-07-06 14:07:00
tags: [SHELL, 最佳实践]
categories: SHELL
---

# SHELL最佳实践

### 错误停止

```bash
#!/bin/bash
set -o nounset
set -o errexit
```

### 函数封装

```bash
log() {
    local prefix="[$(date +%Y/%m/%d\ %H:%M:%S)]"

    echo "${prefix} $@" >&2
}
```

### 使用readonly和local修饰变量

### 使用$()代替``

```bash
# both commands below print out:A-B-C-D
echo "A-`echo B-\`echo C-\\\`echo D\\\'\``"
echo "A-$(echo B-$(echo C-$(echo D)))"
```

### 使用[[]]代替[]

```bash
["${name}" \> "a" -o ${name} \< "m"]
[["${name}" > "a" -o ${name} < "m"]]
```

### echo不是唯一的调试方法

```bash
bash -n my.sh
bash -v my.sh
bash -x my.sh
# 或者
set -o verbose
set -o xtrace
```
