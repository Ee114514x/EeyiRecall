# EeyiRecall

可逆压缩：摘要里的每一行都带原文行号，每一处省略都写明原文行号区间，需要时按区间精确回读。零依赖，单文件，Python 3.8+。

针对长日志、构建输出、大文件「想省上下文又不敢丢原文」的场景：先看压缩结果，确认要看哪几行，再 `fetch` 回完整内容。

## 安装

```bash
python3 recall.py install                  # 自动找最近的 .aicode/skills
python3 recall.py install --scope ~/.aicode/skills
```

也可以直接当命令用：

```bash
<命令> 2>&1 | python3 recall.py trim
```

## 命令

| 命令 | 用途 |
|---|---|
| `trim` | 可逆压缩：保留行带原文行号，省略处标原文区间 |
| `fetch` | 按原文行号区间回读，如 `fetch app.log 401-430` |
| `map` | 输出保留/省略区间清单（JSON），便于程序处理 |
| `batch` | 批量紧凑读多个文件 |
| `check` | 输出文件 sha256 与行数，回读前核对 |
| `install` | 安装为技能 |
| `selftest` | 内置 15 项单测 |

## 效果

同一份 200 行日志：

```text
$ python3 recall.py trim < demo.log
recall trim: 200→36 行, ~882→239 token (+73%)
recall: 可回读区间 13-56, 60-62, 64-119, 122-122, 124-188
1| INFO step 1 ok
2| INFO step 2 ok
...
57| ERROR worker 57: connection refused to db-1
...
```

行号是**原文行号**，可以直接拿回去读：

```bash
$ python3 recall.py fetch demo.log 118-124
```

省略标记里的 `13-56` 也是原文区间，不是相对量。所以摘要可以安全地当索引用，改代码时再回原文。

## 规则

- 改代码、改配置、打补丁，必须 `fetch` 原文；`trim` 仍是摘要。
- 回读前先 `check` 核对 sha256，文件变了行号可能失配。
- 长行被截断时行号保留，`fetch` 那一行能拿回完整内容。

## 自检

```bash
python3 recall.py selftest    # 15 项单测
```

## 相关

[Eeyi](https://github.com/Ee114514x/Eeyi) 是这一系列的非可逆版本，额外提供目录 digest、JSON 结构压缩和 token 估算。两者可串联：Eeyi 决定保留什么，Recall 保证保留的部分能回原文。
