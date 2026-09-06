# heptagon_w6_MHV 本地编辑与服务器执行

## 仓库边界

- 本地发布仓库：`/Users/hzq/hep_th/Building Intelligent Models from Scratch/heptagon_w6_MHV`。
- Git remote：`https://github.com/xghf233/heptagon_w6_MHV.git`。
- 原始 `HZQ-git` 是独立的历史开发目录，不自动同步、覆盖或修改它的 remote。
- 本仓库只含 Symbol edition；运行目录为 checkout 下的 `nanoinfra-main_symbol/`。
- 服务器 checkout 和 Python 环境路径尚未确认，不能沿用旧项目路径作为已部署事实。

本地编辑代码与文档；所有模型测试、推理、smoke 和训练在服务器经批准执行。
保留整个 Symbol edition，包括 `core` 与 heptagon 依赖的旧 amplitude 通用工具。

## 首次提交：先审阅，再推送

以下命令由用户确认后执行；整理文档不等于授权 AI 提交或推送。
截至 2026-09-07 发布准备阶段为 `main` 分支、尚无提交。空远端对应的
`origin/main [gone]` 在首次推送前不代表代码丢失。

```bash
cd "/Users/hzq/hep_th/Building Intelligent Models from Scratch/heptagon_w6_MHV"
git status --short --branch
git remote -v
git add README.md LICENSE .gitignore AGENTS.md CLAUDE.md LOCAL_REMOTE_WORKFLOW.md nanoinfra-main_symbol
git diff --cached --stat
git diff --cached --check
git diff --cached
```

`git add` 只暂存到本地，不上传。首次提交前 `git diff` 不显示未跟踪文件内容，
因此必须检查暂存区；确认没有密钥、数据、环境、checkpoint、日志或无关内容。
不要用 `git add -f` 绕过忽略规则；有异常先停下核查。

审阅通过后：

```bash
git commit -m "Add heptagon w6 MHV training pipeline"
git push -u origin main
git status --short --branch
```

不使用 force-push。如果身份配置、认证或远端历史报错，先检查具体错误；
不要粘贴访问令牌到聊天或把凭证写入仓库。

## 后续本地修改

先检查 `git status`，保留无关用户改动。按需在干净工作树创建任务分支，
只暂存已审阅文件，检查 `git diff` 和 `git diff --cached` 后提交。
AI 推送、重写历史、删除分支等操作仍需明确确认。
不要在 Mac 自动创建环境、安装依赖或运行模型测试。

## 服务器获取代码

首次部署：在用户选定的服务器父目录执行以下命令，不覆盖已有目录。
这是私有仓库，需使用服务器上已配置的授权方式；不要在命令 URL 中嵌入令牌。

```bash
git clone https://github.com/xghf233/heptagon_w6_MHV.git
cd heptagon_w6_MHV
git status --short --branch
git remote -v
git rev-parse HEAD
```

已有 checkout 更新前，确认 remote 正确、工作树干净，且没有未经协调的本地提交。
仅在确认应更新 `main` 时执行：

```bash
git fetch origin
git switch main
git pull --ff-only origin main
```

若存在未提交修改、分歧历史或快进失败，停下协调，不重置、不强推、不覆盖。
代码通过 Git 同步；不要使用 `rsync --delete`，也不要把整个服务器工作区同步回来。

## 数据、环境与验收

1. 单独交接仓库外的五个转换文件：`words.npy`、`coefficients.npy`、`splits.npz`、
   `metadata.json`、`audit.json`。不必上传 WXF；数据与输出不放入 Git。
2. 确认实际服务器 Python 环境、数据路径和全新的输出路径，不默认创建或升级环境。
3. 从 checkout 内 `nanoinfra-main_symbol/` 运行模块命令，不能只复制项目子目录。
4. 按 [SERVER_RUNBOOK.md](nanoinfra-main_symbol/projects/heptagon_symbol/SERVER_RUNBOOK.md)
   依次执行 CPU 测试、真实数据检查、GPU smoke、恢复对照、tiny-overfit、compile 检查。
5. 每阶段先说明命令、读写位置、预计资源与验收标准，取得用户确认；正式训练另行批准。

记录实际 commit、命令、环境/GPU、退出状态、指标、日志与 checkpoint 路径。
目前没有服务器运行结果，不把“代码已准备”记为“测试通过”。

数据、环境、checkpoint 和日志留在 Git 外；只带回经审阅的小型报告或图。
若服务器直接改了源码，先协调并审阅提交，再通过 Git 带回，避免两地独立修改同一文件。
