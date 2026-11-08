# awk 命令速查表 · awk Cheatsheet

[![Stars](https://img.shields.io/github/stars/Ezra-Zhao/awk-cheatsheet?style=social)](https://github.com/Ezra-Zhao/awk-cheatsheet/stargazers) [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE) ![Languages](https://img.shields.io/badge/languages-3-blue)

Support & more: [GitHub Sponsors](https://github.com/sponsors/Ezra-Zhao) · [Gumroad — templates & workbooks I use](https://ezraflow28.gumroad.com) · [Ko-fi](https://ko-fi.com/ezra777zhao)


> 🌍 [Español](README.es.md) · [Português](README.pt.md)

一份**一页纸**的 awk 速查表：30 条真实可用的用法——`pattern { action }` 的基本形状、字段与两个行号计数器、用正则/区间/比较挑行、干活的内置变量与字符串函数，以及大家真正留在命令历史里的单行配方（求和、均值、去重、分组汇总、双文件 join）。每个条目只讲三件事——**它是干什么的、一条能直接复制粘贴的命令、适用场景标签**。不编造语法：全部 30 条都在 mawk 1.3.4 上对着自造夹具文件**一条一条真实执行、输出核对过**才入表，语义逐项对照 GNU Awk 用户指南；gawk 与 mawk/BusyBox 的实现差异在表末尾专列（其中几条是静默失效，不标出来能坑人一整晚）。

## 面向谁

运维工程师、后端开发者、SRE，以及任何"日志就在眼前，可就是算不出每组总和"、"两个文件想按 key 对上还得写脚本"的人。如果 `grep` 只能筛行、`sed` 只会替换，而你需要的是**看懂列、做四则运算、跨行记账**，这张表就是为你准备的。

## 怎么用

1. 直接看 **[CHEATSHEET.md](CHEATSHEET.md)** ——速查表正文，英文命令全球开发者都无压力。
2. 先分清你手里的 awk 是谁：`awk --version` 第一行会写 gawk 还是 mawk；macOS 自带的是 BSD awk。表内 30 条在 mawk 1.3.4 上验证过，表末的实现差异注记请务必看一眼。
3. 按"基础 → 模式 → 变量与函数 → 配方"的顺序，在自己的练习文件里每个条目亲手敲一遍，一小时就顺手了。

## 先跑起来（30 秒验证环境）

```bash
awk --version | head -1                # 先确认实现：gawk / mawk / BSD，差异点表末尾专列
printf 'alice math 88\nbob math 72\n' > /tmp/scores.txt
awk '{s += $3} END {print s}' /tmp/scores.txt   # 160——第三列求和，这就是 awk 的招牌动作
```

## 一条能直接用的黄金组合

```bash
awk 'NR == FNR {tier[$1] = $2; next} {print $1, $2, tier[$1]}' codes.txt events.txt
```

双文件 join：第一个文件读进内存当查找表（`NR == FNR` 阶段只存不用），第二个文件逐行过一遍、把查到的值拼在行尾。没有它，这件事要么写脚本，要么开数据库；有了它，一行命令。

## 收录了什么

| 分类 | 内容 |
|---|---|
| 🧱 基础 | print 与 $0 整行 / $1、$NF、NF 字段 / NR 与 FNR 两个计数器 / BEGIN 与 END / 模式与动作的默认行为 / 自有变量与 -v 传参（6 条） |
| 🎯 模式 | /正则/ 全行匹配 / /start/,/end/ 区间模式 / 数字与字符串比较的类型陷阱 / ~ 与 !~ 字段匹配 / &&、||、! 组合 / FNR>1 跳每份文件表头（6 条） |
| 🧰 变量与函数 | -F 与 FS 分隔符 / 改字段重建 $0 与 OFS / length / substr / split / sub 与 gsub / printf 与 sprintf / match 配 RSTART、RLENGTH / toupper、index（9 条） |
| 🍳 配方 | 列求和 / 带空文件防线的均值 / !seen[$0]++ 保序去重 / 分组汇总 / 词频统计 / NR==FNR 双文件 join / CSV 抽列重排 / CSV 引号警告 / 最长行（9 条） |

官方文档只链 https://www.gnu.org/software/gawk/manual/，所有命令均对照 GNU Awk 用户指南核实过语义，并在本机 mawk 1.3.4 上全部实跑通过；示例只在自造的夹具文件里操作，不涉及任何真实数据。

## 为什么是这 30 条

- **计数器分工先讲清**：NR 与 FNR 只差一个字母，却管着"跳表头"和"双文件 join"两件大事——用错 NR 跳表头，第二份文件的表头会静默混进数据里，这类坑表里都标了 ⚠️ 并给正解。
- **比较的类型陷阱必须上榜**：`"010" == 10` 为真、`"010" == "10"` 为假——比较常量带不带引号决定按数字还是按字符串比，这是 awk 最反直觉的一条，不讲清迟早踩。
- **实现差异不藏着**：`gensub`、`BEGINFILE`、`FPAT` 是 gawk 专有；mawk 对 `gensub` 会报错，但对后两者是**静默忽略**——脚本照跑、结果悄悄不对。表末尾逐项列明，附本机实测结论。
- **配方只收真留在历史里的**：求和、去重、分组、join 这四件占了日常 awk 用量的大头，每条都给了可直接改字段号套用的形态。

## 欢迎 PR

发现命令写错了、参数过时了、翻译不到位——欢迎提 Pull Request。新增条目请保持统一格式（一句话说明 / bash 代码块 / 标签），**只收真实可用的命令，不确定的宁可不收**。

## 📚 更多速查表

同一作者的其他速查表，同样有中/西/葡三语版本：

- [SSH 命令速查表](https://github.com/Ezra-Zhao/ssh-cheatsheet) — 28 条连接、密钥、隧道与加固命令
- [FFmpeg 命令速查表](https://github.com/Ezra-Zhao/ffmpeg-cheatsheet) — 28 条音视频处理命令
- [Git 实战速查表](https://github.com/Ezra-Zhao/git-cheatsheet) — 30 条日常+救命命令
- [Docker 命令速查表](https://github.com/Ezra-Zhao/docker-cheatsheet) — 28 条容器命令
- [curl 命令速查表](https://github.com/Ezra-Zhao/curl-cheatsheet) — 29 条 API 调试命令
- [正则表达式速查表](https://github.com/Ezra-Zhao/regex-cheatsheet) — 30 条实战正则模式
- [jq 命令速查表](https://github.com/Ezra-Zhao/jq-cheatsheet) — 30 条 JSON 处理 filter
- [终端神器速查表](https://github.com/Ezra-Zhao/terminal-superpowers-cheatsheet) — 14 个终端效率工具
- [Adobe 免费替代速查表](https://github.com/Ezra-Zhao/adobe-free-alternatives-cheatsheet) — 16 个 Adobe 免费替代品
- [退订省钱速查表](https://github.com/Ezra-Zhao/desubscribe-cheatsheet) — 10 组退订替代，年省约 $1,260
- [kubectl 命令速查表](https://github.com/Ezra-Zhao/kubectl-cheatsheet) — Kubernetes 常用命令速查
- [SQL 查询速查表](https://github.com/Ezra-Zhao/sql-cheatsheet) — 常用 SQL 查询与函数速查
- [Vim 命令速查表](https://github.com/Ezra-Zhao/vim-cheatsheet) — 30 条编辑、搜索替换与宏命令
- [tmux 命令速查表](https://github.com/Ezra-Zhao/tmux-cheatsheet) — 30 条会话、窗口与窗格命令
- [systemd 命令速查表](https://github.com/Ezra-Zhao/systemd-cheatsheet) — 30 条服务管理与日志命令
- [find + xargs 命令速查表](https://github.com/Ezra-Zhao/find-xargs-cheatsheet) — 30 条找文件与批量执行命令

## License

MIT © 2026 Guangyi Zhao，详见 [LICENSE](LICENSE)。

📣 Telegram 频道：https://t.me/EzraZhao777（各平台的更新都同步到这里）
