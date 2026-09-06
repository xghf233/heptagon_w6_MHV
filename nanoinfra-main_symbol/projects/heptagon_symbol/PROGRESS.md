# Heptagon 三圈 MHV — 当前进度

最后更新：2026-09-07。

当前状态：**本地数据转换已执行并通过数据完整性审计；训练工程代码和测试已编写，尚未执行服务器验证或训练。**
“代码已编写”不等于“测试通过”，也不代表已获得模型精度、耗时或显存结果。

## 1. 工程位置与复用边界

开发阶段是在现有 `HZQ-git/nanoinfra-main_symbol` 内新增 `projects/heptagon_symbol/`，
没有从头实现 Transformer。发布阶段将完整 Symbol edition 复制到独立的
`heptagon_w6_MHV` Git 仓库，保留 `nanoinfra-main_symbol/` 层级。
它仍与旧 `projects/amplitude_symbol/` 并列，共享同一版 `core/`。
以下实现与数据记录保留开发历史；新发布副本不自动回写原始 `HZQ-git`。

发布准备（2026-09-07）：已配置新仓库 remote，补充根 README、协作约定、
服务器路径占位说明和忽略规则。本次仅整理文档及忽略规则，未修改模型源码或配置；
尚未暂存、提交或推送，服务器 checkout 和环境路径待部署时确认。

| 部分 | 本次处理 |
| --- | --- |
| GPT 主体、模型组装、AdamW 参数组、LR 调度、KV-cache、DCP | 直接复用既有 `core`，未修改其实现 |
| word-bidirectional mask 工具 | 复用旧 `amplitude_symbol/blocks/attention.py` 的通用函数，显式传入 `[1,7)`；未修改该文件 |
| heptagon alphabet、转换器、tokenizer、dataset | 新增项目专用模块，不改旧十字母输入和词表 |
| 训练入口、评估和 checkpoint 封装 | 新增项目专用实现；训练循环未直接调用旧 `core.training.trainer.Trainer` |
| 配置与测试 | 新增 `configs/` 和 `tests/`，不替换旧实验配置 |
| Symbol 版 `pyproject.toml` | 前序工作已增加 heptagon 包发现和 YAML 配置打包；未新增依赖 |
| README、计划、进度导航 | 更新，明确两条实验线和验证状态 |
| standard 版、旧训练结果、原 WXF | 本次 heptagon 工作未修改 |

单独项目化的原因：三圈 heptagon 的 42-letter alphabet、六字母 word、1048 词表、
随机行切分与旧项目的六字母 alphabet、十字母 word、1012 词表、D3 轨道切分不同。
不能仅替换旧数据路径或直接沿用旧 mask 默认值。

项目内单卡训练循环负责显式样本预算、全量/子集验证区分和完整 checkpoint。
仍复用原模型、优化器、调度和存储机制；没有改写共享 Trainer 或强行迁移旧实验。
由于 mask 工具仍从旧项目导入，heptagon 文件夹不是可脱离整个 Symbol edition 独立运行的包。

## 2. 已完成的执行工作：数据转换

- 输入：`hep_w6_phy.wxf`，仅处理三圈 weight 6 MHV；未启动 Wolfram 或符号求值。
- 保留 467,250 个唯一非零项，word 长度 6，42 个字母，33 种系数，范围 −48～48。
- 固定 PCG64 seed=42 随机行切分：train 373,800 / val 46,725 / test 46,725。
- 不做轨道分组、对称压缩或增强；训练集覆盖全部33种系数值。
- 数组保存后读回、全局查重、切分覆盖/互斥，以及重建完整 WXF 表达式字节的哈希核验均通过。
- 实测转换约6.90秒，峰值RSS约121.11MiB；五个产物总计5,624,185 bytes。

数据目录（仓库外）：

```text
/Users/hzq/hep_th/Building Intelligent Models from Scratch/nanoinfra-artifacts/heptagon_symbol/w6/v1/
```

输入/输出哈希和详细审计分别在产物 `metadata.json` 与 `audit.json`。
该审计证明转换数据完整，不替代后来新增 tokenizer、训练或 checkpoint 的运行验证。

## 3. 已编写、待验证的工程

| 模块 | 内容 | 执行验证状态 |
| --- | --- | --- |
| `dataset.py` | 数组/哈希检查、固定 split、CPU 预编码、逐轮洗牌、恢复采样位置 | 待服务器测试 |
| `tokenizer.py` | 1048词表、base-1000、长度16序列、sign/magnitude/EOS监督、严格解码 | 待服务器测试 |
| `model.py` | 复用GPT，编译前安装 `[1,7)` mask，检查运行时配置 | 待服务器测试 |
| `train.py` | 单卡bf16训练、日志、显式步数预算、验证和保存 | 待服务器 smoke |
| `evaluator.py` | 长度8无标签prompt、贪心自由生成、exact/sign/magnitude/invalid及分组指标 | 待服务器测试 |
| `checkpoint.py` | 模型/优化器、数据进度、RNG、训练配置和数据/代码哈希 | 待恢复对照 |
| `eval_checkpoint.py` | 独立恢复评估；默认val，test需显式启用 | 待服务器测试 |
| `tests/` | 编码、无泄漏负对照、缓存生成、配置、参数计数、恢复后下一步更新等 | 已编写，未运行 |

本地只进行了源码静态审阅与差异空白检查，没有执行 Python 单测、模型推理、smoke 或训练。
也未在服务器安装依赖、运行计算、提交或推送这些改动。

## 4. 已准备的运行配置（不是训练结果）

| 配置 | 模型 | batch | 预算 | 用途 |
| --- | --- | ---: | ---: | --- |
| `smoke` | 4层 / 512维 / 8 heads | 8 | 20步 | 检查训练、评估和保存路径 |
| `tiny_overfit` | 同上 | 32 | 1000步、固定32项train | 要求全32项自由生成exact=1，否则报失败 |
| `w6_random` | 同上 | 512 | 73,008步，约100次train遍历 | 首轮正式实验 |

正式模型参数量预计13,657,600，已编写计数测试，实际组装后再核对。
周期验证固定4096项val；全量val为46,725项，只有全量val exact改善才更新best。
训练不自动评估test，不将随机切分结果与旧D3隔离实验当成同难度比较。

## 5. 下一步与验收顺序

1. 用户审阅代码，协调本地/服务器Git版本与只读数据传输。
2. 服务器CPU单测，再进行真实转换产物加载验证（不能以skip算通过）。
3. GPU eager smoke与连续20步/分段10+10步恢复对照。
4. Tiny-overfit与独立compile smoke。
5. 实测耗时、显存、磁盘与全量验证成本后，单独批准正式训练。
6. 根据全量val选择checkpoint；协议锁定后另行批准test评估。

每阶段命令、读写范围和验收条件见 [SERVER_RUNBOOK.md](SERVER_RUNBOOK.md)。
接口见 [README.md](README.md)，完整设计和实施历史见
[HEPTAGON_W6_PLAN.md](../amplitude_symbol/docs/HEPTAGON_W6_PLAN.md)。
后续实际运行结果应记录命令、代码版本、环境、退出状态和产物路径，再更新本进度。
