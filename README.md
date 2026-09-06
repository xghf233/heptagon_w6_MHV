# Heptagon w6 MHV

学习三圈（weight 6）MHV heptagon symbol 的非零系数：输入六个字母组成的 word，
用 Transformer 预测带符号的 base-1000 系数序列。

本仓库由 `HZQ-git` 中的 Symbol edition 整理而来，是独立的
[`xghf233/heptagon_w6_MHV`](https://github.com/xghf233/heptagon_w6_MHV) 仓库，
与 `nanoinfra-git` 并列。保留 `nanoinfra-main_symbol/` 这一层目录，
不需要另外复制一套模型，也不能仅取出 `projects/heptagon_symbol/` 独立运行。

## 目录与入口

- [`nanoinfra-main_symbol/projects/heptagon_symbol/`](nanoinfra-main_symbol/projects/heptagon_symbol/README.md)：本次数据、tokenizer、训练、评估与恢复代码。
- [`nanoinfra-main_symbol/core/`](nanoinfra-main_symbol/core/)：复用的模型与基础设施。
- [`nanoinfra-main_symbol/projects/amplitude_symbol/`](nanoinfra-main_symbol/projects/amplitude_symbol/README.md)：旧实验与通用 attention 工具；heptagon 仍依赖其中的工具，不能直接删除。
- [当前进度](nanoinfra-main_symbol/projects/heptagon_symbol/PROGRESS.md)、[服务器运行手册](nanoinfra-main_symbol/projects/heptagon_symbol/SERVER_RUNBOOK.md)、[本地与服务器协作](LOCAL_REMOTE_WORKFLOW.md)。

服务器从 **仓库内的 `nanoinfra-main_symbol/`** 运行模块入口，而不是进入项目子目录直接运行脚本。
环境、数据和输出绝对路径确认后，按运行手册逐阶段执行；不要把示例路径原样使用。

## 数据与实验设计

| 项目 | 当前方案 |
| --- | --- |
| 数据 | 467,250 个唯一非零项；42 个字母；每个 word 长度 6 |
| 系数 | 33 种整数值，范围 −48～48；不包含零系数训练样本 |
| 切分 | 随机行切分，seed=42；train/val/test = 373,800 / 46,725 / 46,725 |
| 先验处理 | 不按轨道分组，不做对称压缩或增强；相关 word 可以跨 split |
| 编码 | 1048-token 词表，符号 + base-1000 数值块 + EOS |
| 首轮模型 | 4 层、512 维、8 heads；预计 13,657,600 参数，待运行核对 |
| 正式预算 | batch=512，73,008 次更新，约 100 次训练集遍历；尚未执行 |

截至 2026-09-07，本地转换与完整性审计已通过；新增训练代码和测试已编写，
**尚未执行服务器验证或训练**。旧 amplitude 项目的精度不是本项目的结果。
当前任务只评估非零样本的系数预测，不能据此声称已学会判断任意 word 是否为零。

## 数据交接与执行边界

Git 仅保存代码、配置、文档和经审阅的小型报告。数据、虚拟环境、checkpoint、
日志和运行输出不随代码上传；`.gitignore` 不能替代提交前的内容审阅。

已转换的数据位于本地仓库外：

```text
/Users/hzq/hep_th/Building Intelligent Models from Scratch/nanoinfra-artifacts/heptagon_symbol/w6/v1/
```

单独传输其中的 `words.npy`、`coefficients.npy`、`splits.npz`、`metadata.json`、
`audit.json`（总计 5,624,185 bytes），保留原样并在服务器验证哈希。
训练不需要原始 WXF、Wolfram 或 AIAmplitudes 外部代码仓库。

本地只做源码与文档准备；安装依赖、单测、smoke、模型推理和训练在服务器经批准执行。
服务器 checkout、环境与数据路径目前尚未确认。先 CPU 测试，再 smoke、恢复对照、
tiny-overfit 和 compile 检查，最后单独确认正式训练预算。

## License

保留原工程的 [MIT License](LICENSE)。
