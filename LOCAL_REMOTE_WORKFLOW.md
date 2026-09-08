# heptagon_w6_MHV 本地开发与服务器运行

## 仓库与职责

当前科研项目使用一条主线：**本地 heptagon_w6_MHV → GitHub 同名仓库 → 服务器运行副本**。

- 本地开发目录：`/Users/hzq/hep_th/Building Intelligent Models from Scratch/heptagon_w6_MHV`。
- GitHub remote：`https://github.com/xghf233/heptagon_w6_MHV.git`。
- 本仓库只含 `nanoinfra-main_symbol/`，保留 core 及 heptagon 依赖的 amplitude 工具。
- `HZQ-git` 是独立历史工作区，不自动同步或修改它；standard 版不属于本仓库。
- 数据、环境、checkpoint 和完整输出放在仓库外；本地备份使用同级 `nanoinfra-artifacts/`。
- 服务器当前 checkout 和 Python 环境路径需要连接后核对，不能把旧项目路径当作当前部署事实。

本地直接准备源码与文档；所有项目单测、模型推理、smoke 和训练仍按 AGENTS.md 在服务器执行。
Docker 任务副本不再是每次编辑的必经步骤。需要更强隔离的任务可单独采用容器。

## 本次基线与结果

2026-09-07 只读核查时，本地及 GitHub main 均为 `9bae12d`，首次发布已经完成。
服务器配置校验修复和首轮报告先接回 HZQ-git，随后整理进本仓库工作区。
这次本地整理不包含暂存、提交或推送；版本是否已发布应以实际 Git 状态为准。

首轮训练与模型位置见 [PROGRESS.md](nanoinfra-main_symbol/projects/heptagon_symbol/PROGRESS.md)，
证据边界见 [导入核对](nanoinfra-main_symbol/projects/heptagon_symbol/reports/SERVER_IMPORT_2026-09-07.md)。
原手册的“首次提交待完成”“尚无运行结果”等待办不再适用。

## 本地修改与发布

只读检查可以直接进行：

```bash
git status --short --branch
git diff --stat
git diff
git diff --cached
```

按用户当前要求，修改文件、切换分支、暂存、提交或推送之前，先说明具体范围并取得批准。
一个阶段的批准不自动覆盖下一阶段。较大任务可在获准后建立任务分支或 worktree，
但 worktree 只隔离工作目录和改动，不替代权限沙盒。

完成修改后，核对所有新增文件，不能只看 git diff（默认不显示未跟踪文件内容）。
只暂存审阅过的代码、配置、文档、紧凑指标和选定图表；不使用 git add -f 绕过忽略规则。
随后检查暂存差异，获准后提交。推送前重新核对远端分支与待推送提交，单独获得确认。
不强推，不把 HZQ-git 的无关历史或整个工作区混入本仓库。

## 服务器获取已发布版本

第一次在选定的新目录 clone；已有 checkout 先核对 remote、分支、HEAD、未提交修改，
以及目标运行目录是否正在被训练使用。确认可以更新后获取用户指定的分支或提交。
服务器源码有未提交修改、分歧提交或来源不明文件时，先协调，不重置或覆盖。

以下是更新已有 main 的示例，需先满足上述条件并获准：

```bash
git fetch origin
git switch main
git pull --ff-only origin main
git rev-parse HEAD
```

将最终 HEAD 与选定的已发布提交核对；不要仅记录“最新 main”。
正式训练使用固定代码版本，运行过程中不更新它正在使用的 checkout。
运行入口从 `nanoinfra-main_symbol/` 执行，不能只复制 `projects/heptagon_symbol/`。

## 环境、数据与实验

优先复用服务器现有环境与仓库外数据，不在更新代码时复制 venv 或自动升级依赖。
历史 run.json 记录的是 PyTorch 2.12.1+cu130、NumPy 2.4.6、RTX 5090，
不代表当前服务器环境已经重新检查。新实验采用尚不存在的输出目录。

根据 [SERVER_RUNBOOK.md](nanoinfra-main_symbol/projects/heptagon_symbol/SERVER_RUNBOOK.md)
选择与修改相关的最小验证；说明命令、输入输出、资源和验收条件，获准后执行。
每次实验记录代码 commit、配置、数据版本、环境、命令、结果与 checkpoint 路径。
服务器报告与本地核验结论分开记录；缺失的单测输出不能当作通过。

## 接回修复与结果

服务器若修改源码，先审阅差异，获准后提交到修复分支并经 GitHub 接回本地。
避免两端同时独立修改同一文件。用户提供的离线补丁包可在明确授权后核对合入，
保留来源、基线、补丁和哈希；不将压缩包直接覆盖整个仓库。

报告、曲线和紧凑指标审阅后放入项目 reports；完整日志、逐项预测和模型按运行名称
保存到 artifacts。模型备份保留完整 checkpoint 目录，不能只复制张量文件。
不使用 rsync --delete，不把整个服务器工作区同步回来覆盖本地。
