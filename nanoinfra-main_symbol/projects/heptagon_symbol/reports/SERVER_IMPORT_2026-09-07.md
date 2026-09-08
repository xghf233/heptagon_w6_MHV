# 服务器结果合入主仓库 — 2026-09-07

`heptagon_w6_MHV` 现为当前科研项目的主开发仓库：本地编辑，通过 GitHub 发布版本，
服务器获取对应代码执行。本文件记录服务器下载包从 HZQ-git 接收区合入本仓库的来源、
文件对应关系和验证范围。原服务器报告原样保存在 [ACCEPTANCE_REPORT.md](ACCEPTANCE_REPORT.md)。

## 来源和合入范围

服务器代码包声明基于本仓库提交 `9bae12d21c86c2f7e4a7532a73bc7092333fd80e`，
另有未提交的 `train.py` 配置校验修复。两份下载包最初已导入仓库外 artifacts，
代码先在 HZQ-git 核对；本阶段按用户批准的范围更新本地 heptagon_w6_MHV。
此次未修改 HZQ-git，未切换分支、暂存、提交或推送。

| 原下载包 | SHA-256 |
| --- | --- |
| `heptagon_w6_server_code_changes.tar.gz` | `e40ac72fb48820a245b96aaf091d8b7c06d8077bcb2c078dee0f70f7e9692efe` |
| `heptagon_w6_run.tar` | `67347f93366854610865432bae0967e528e4f4e87caaa884a5e7211fc3db1e4b` |

合入前两份 Symbol 工程有115个文件完全一致；已有训练源码只需接回 `train.py` 的修复。
现在该文件逐字节匹配服务器的 `train.py.server`：允许额外顶层 `hydra` 键，其他缺失或
未知键继续严格校验。共享 core、amplitude 工程、训练配置和既有测试源码保持不变。

新增 `make_report.py`、本报告目录中的服务器报告、曲线和紧凑指标 JSON。
README、PROGRESS、运行手册以及根目录 AGENTS/协作手册更新为主仓库定位和当前状态。
原根目录 `ACCEPTANCE_REPORT.md` 改为报告导航；根目录旧图片予以保留。
没有复制 HZQ-git 的 standard 版、空数据目录或重复的项目根目录报告。

## 模型和数据

现有完整产物在本地仓库外，未在本阶段复制或修改：

```text
/Users/hzq/hep_th/Building Intelligent Models from Scratch/nanoinfra-artifacts/heptagon_symbol/
├── w6/v1/                                  # 五个已转换的数据文件
└── 2026-09-07/
    ├── import-manifest.json                 # 原包与65个解包文件的校验清单
    ├── verification.json                   # 第一阶段导入核对
    ├── heptagon_w6_server_code_changes/     # 原样代码改动包
    └── heptagon_w6_run/
        ├── checkpoints/step_073000/        # best
        ├── checkpoints/step_073008/        # 最终
        ├── run.json / summary.json / history.jsonl
        ├── best-val.json / best-test.json
        ├── val_*.json / train_diagnostic_*.json
        └── report/
```

两份 checkpoint 均保留 DCP 张量、`.metadata` 和 `meta.json`；中间19个 checkpoint
没有包含在下载包中。模型文件此次未反序列化或加载。
metadata 中的服务器绝对路径保持原样。后续在服务器复评时显式指定实际
`--checkpoint`、`--data-dir`、`--output`，不要修改哈希绕过兼容性校验。
最终 checkpoint 已完成原定预算，不能直接假设更改 max_steps 后可以原样续训。

## 可核对的运行记录

| 项目 | 随包记录 |
| --- | --- |
| 运行 / 切分 | `w6-random-seed42` / random-row，seed=42 |
| 样本数 | train 373,800 / val 46,725 / test 46,725 |
| 参数 / 完成步数 | 13,657,600 / 73,008 |
| GPU / CUDA | NVIDIA GeForce RTX 5090 / 13.0 |
| PyTorch / NumPy | 2.12.1+cu130 / 2.4.6 |
| 耗时 / 峰值显存 | 837.925秒 / 941,300,224 bytes（约0.877 GiB） |
| best / 最终 | `step_073000` / `step_073008` |
| 独立 val / test exact | 各46,725/46,725，即1.0 |
| 每步输入 / 监督 token | 7,680 / 1,536 |

紧凑指标见 [w6_random_seed42_summary.json](w6_random_seed42_summary.json)。
`summary.test_evaluated=false` 是训练结束时的状态；独立 test 结果另存于 `best-test.json`。
`acceptance=not_requested` 也不是整套 runbook 的验收状态。
数据 metadata SHA-256 为 `a1e7f0d579c6d1a76e29ccf6157ca3e41c563c07c95fb3ff4538264c17448665`。

## 本阶段检查和限制

合入后核对 run.json 与两个 checkpoint 的20个源码哈希，确认它们全部匹配本仓库。
同时复核既有数据 manifest、归档文件哈希、源码静态语法、文档链接与改动范围。
这些检查证明收到的代码和记录相互一致，不是重新运行模型或独立认证服务器执行过程。

本阶段没有执行项目单测、smoke、推理、训练、依赖安装或模型下载。
服务器报告称 smoke、连续/分段恢复、tiny-overfit 和 compile smoke 已通过，但相关
完整原始输出及 CPU pytest 结果未随包提供，因此本地没有独立验证全部验收阶段。
原报告中的“无泄漏”或退火因果判断不能由文件一致性证明；random-row 单 seed 的
结果也不能直接推广到 orbit-grouped、跨 weight 或零系数泛化。

本阶段改动前备份及最终差异清单位于仓库外：

```text
/Users/hzq/hep_th/Building Intelligent Models from Scratch/nanoinfra-artifacts/heptagon_symbol/main-repository-sync-20260907-160936/
```

该目录保存原文件、Git 状态、文件清单、`changes.diff` 和 `verification.json`。
后续暂存、提交和 GitHub 推送分别由用户确认。
