
# ARIS: Auto Claude Code Research Skills

ARIS 是一组面向 AI 辅助科研的 Claude Code / Codex 技能集合。它把文献调研、想法生成、实验实现、自动评审、论文写作、rebuttal 等流程拆成可复用的 `SKILL.md` 工作流，让 AI agent 能在一个项目目录里按步骤推进研究任务。

> 简单说：ARIS 不是一个新的平台，而是一套可安装到 Claude Code / Codex / Cursor 等 agent 工具里的研究工作流模板。

## 适合做什么

ARIS 主要适合以下场景：

- 调研某个研究方向，整理已有工作和潜在 gap
- 基于文献和已有代码生成研究想法
- 把实验计划落地成可运行代码
- 自动运行实验、收集结果、分析失败原因
- 对论文草稿做多轮评审和修改
- 从实验结果生成论文结构、图表、LaTeX 初稿
- 根据审稿意见生成 rebuttal 草稿
- 在项目中沉淀长期研究记忆，例如读过的论文、失败的想法、实验结论

## 核心设计

ARIS 的核心由两部分组成：

1. **执行模型**  
   通常是 Claude Code、Codex CLI、Cursor、Trae 等负责读写文件、改代码、运行命令的 agent。

2. **评审模型**  
   通常是 Codex MCP 或其他 LLM，用于做外部评审、挑错、审实验、审论文。  
   这样可以避免单一模型“自己写、自己审”导致的盲区。

所有技能都是普通 Markdown 文件：

```text
skills/
  idea-discovery/
    SKILL.md
  experiment-bridge/
    SKILL.md
  auto-review-loop/
    SKILL.md
  paper-writing/
    SKILL.md
  rebuttal/
    SKILL.md
```

你可以直接阅读、修改、复制这些技能，也可以只安装其中一部分。

---

## 功能概览

### 1. Idea Discovery

从一个研究方向出发，自动完成：

- 文献搜索和整理
- 已有工作的局限分析
- 研究想法生成
- 初步 novelty check
- 可行性评估
- 输出候选 idea 和后续实验建议

常用命令：

```text
/idea-discovery "discrete diffusion language models"
/research-lit "LLM post-training for reasoning"
/idea-creator "efficient agent training"
```

### 2. Experiment Bridge

把实验计划转成代码和运行任务：

- 读取实验计划
- 修改或生成实验脚本
- 做基本 sanity check
- 可选：让外部 reviewer 检查实验代码
- 在本地、远程 GPU 或云 GPU 上启动实验
- 收集结果并更新实验记录

常用命令：

```text
/experiment-bridge "EXPERIMENT_PLAN.md"
/run-experiment train.py --lr 1e-4 --epochs 20
/monitor-experiment
```

### 3. Auto Review Loop

对论文、实验报告或研究方向做多轮自动评审：

- 外部模型审稿
- 识别主要问题
- 自动执行可修复项
- 必要时补实验
- 重新评审
- 输出每轮评分和修改记录

常用命令：

```text
/auto-review-loop "our method for factorized reasoning"
/research-review "paper/"
```

### 4. Paper Writing

从研究叙事和实验结果生成论文草稿：

- 生成 paper outline
- 建立 claim-evidence matrix
- 生成图表和表格
- 编写 LaTeX
- 编译 PDF
- 做格式检查和内容改进

常用命令：

```text
/paper-writing "NARRATIVE_REPORT.md"
/paper-plan "NARRATIVE_REPORT.md"
/paper-write
/paper-compile "paper/"
```

### 5. Rebuttal

根据审稿意见生成 rebuttal：

- 解析 review
- 拆分 reviewer concern
- 制定回应策略
- 检查是否存在编造、过度承诺、遗漏问题
- 输出可直接粘贴版本和扩展编辑版本

常用命令：

```text
/rebuttal "paper/ + reviews.md" — venue: ICML, character limit: 5000
```

### 6. Research Wiki

为项目建立长期研究记忆：

- 记录读过的论文
- 记录 idea 的成功和失败原因
- 记录实验结果
- 维护 claim 和 evidence 的关系
- 避免反复探索已经失败的方向

常用命令：

```text
/research-wiki init
/research-wiki stats
/research-wiki query "diffusion LM"
```

---

## 安装前准备

### 必需

- Git
- Claude Code 或其他支持 `SKILL.md` 的 agent 工具
- 一个本地项目目录

### 推荐

如果你需要自动评审、论文审稿、rebuttal stress test 等能力，建议安装 Codex CLI 作为 reviewer：

```bash
npm install -g @openai/codex
codex setup
```

然后把 Codex MCP 加到 Claude Code：

```bash
claude mcp add codex -s user -- codex mcp-server
```

### 可选

如果你要使用对应功能，还需要：

| 功能 | 依赖 |
|---|---|
| 论文 PDF 编译 | LaTeX、latexmk、pdfinfo |
| AI method figure | Gemini API key 或其他绘图后端 |
| 远程实验 | SSH 到 GPU 机器 |
| Vast.ai 租 GPU | vastai CLI 和 Vast.ai API key |
| Zotero 文献库 | Zotero MCP |
| Obsidian 笔记库 | Obsidian/MCP vault 工具 |

LaTeX 安装示例：

```bash
# macOS
brew install --cask mactex
brew install poppler

# Ubuntu / Debian
sudo apt install texlive-full latexmk poppler-utils

# 验证
latexmk --version
pdfinfo -v
```

---

## 快速开始

### 1. 克隆 ARIS

建议把 ARIS 克隆到一个稳定位置：

```bash
git clone https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep.git ~/aris
```

### 2. 在你的研究项目里安装技能

进入你的项目目录：

```bash
cd ~/your-research-project
bash ~/aris/tools/install_aris.sh
```

安装后会在项目中生成类似结构：

```text
your-research-project/
  .claude/
    skills/
      idea-discovery -> ~/aris/skills/idea-discovery
      experiment-bridge -> ~/aris/skills/experiment-bridge
      auto-review-loop -> ~/aris/skills/auto-review-loop
      paper-writing -> ~/aris/skills/paper-writing
      ...
  .aris/
    installed-skills.txt
  CLAUDE.md
```

ARIS 默认采用项目级安装，这样不同项目之间不会互相污染，也不会覆盖你全局的个人技能。

### 3. 启动 Claude Code

```bash
claude
```

在 Claude Code 里测试：

```text
/idea-discovery "your research direction"
```

---

## 常用工作流

### 从零开始探索一个方向

```text
/idea-discovery "efficient test-time scaling for language model agents"
```

输出通常包括：

- 相关工作总结
- 研究 gap
- 候选 idea
- novelty 风险
- 可执行实验建议
- 下一步推荐

### 已经有实验计划，开始实现

```text
/experiment-bridge "EXPERIMENT_PLAN.md"
```

建议你的 `EXPERIMENT_PLAN.md` 至少包含：

```markdown
# Experiment Plan

## Goal

What claim are we testing?

## Baseline

Existing method or codebase.

## Proposed Method

What should be changed?

## Metrics

How to evaluate success.

## Runs

- baseline
- proposed
- ablation 1
- ablation 2
```

### 自动评审论文或报告

```text
/auto-review-loop "focus on method novelty and experiment validity"
```

它会读取当前项目里的论文、实验结果、报告和相关上下文，进行多轮 review 和修复。

### 生成论文草稿

准备一个 `NARRATIVE_REPORT.md`：

```markdown
# Narrative Report

## Problem

What problem does this work solve?

## Method

What is the proposed method?

## Claims

- Claim 1:
- Claim 2:

## Evidence

Experiments, metrics, tables, figures.

## Limitations

Known limitations and failure cases.
```

然后运行：

```text
/paper-writing "NARRATIVE_REPORT.md"
```

输出通常位于：

```text
paper/
  main.tex
  sections/
  figures/
  references.bib
  main.pdf
```

### 处理审稿意见

把 review 保存成 `reviews.md`，然后：

```text
/rebuttal "paper/ + reviews.md" — venue: ICML, character limit: 5000
```

输出通常包括：

```text
PASTE_READY.txt
REBUTTAL_DRAFT_rich.md
```

---

## 项目配置

建议在项目根目录维护 `CLAUDE.md`，告诉 agent 你的项目约束、环境和实验习惯。

示例：

```markdown
# Project Instructions

## Research Topic

We study efficient reasoning-time scaling for LLM agents.

## Codebase

Main training code is under `src/`.
Experiment configs are under `configs/`.

## Environment

Use Python 3.10.
Activate environment with:

```bash
conda activate research
```

## GPU

Use local GPUs by default.

## Logging

Save all experiment outputs to `runs/`.
Use JSONL logs for metrics.

## Paper

Paper source is under `paper/`.
Use `latexmk -pdf main.tex` to compile.
```

如果你要用远程 GPU，可以写：

```markdown
## Remote GPU

- gpu: remote
- SSH: `ssh my-gpu-server`
- GPU: 4x A100
- Conda env: `research`
- Activate: `conda activate research`
- Code directory: `/home/user/experiments/my-project`
- Use `screen` or `tmux` for long-running jobs
```

---

## 常用参数

多数技能支持在命令后追加参数：

```text
/research-pipeline "topic" — effort: max
/auto-review-loop "paper/" — human checkpoint: true
/paper-writing "NARRATIVE_REPORT.md" — venue: NeurIPS
/research-lit "topic" — sources: local, web
```

常见参数：

| 参数 | 默认值 | 说明 |
|---|---:|---|
| `effort` | `balanced` | 工作强度：`lite`、`balanced`、`max`、`beast` |
| `human checkpoint` | `false` | 是否在关键节点等待人工确认 |
| `sources` | `all` | 文献来源，例如 `local`、`web`、`zotero` |
| `venue` | `ICLR` | 论文目标会议或期刊 |
| `gpu` | `local` | GPU 模式：`local`、`remote`、`vast` |
| `code review` | `true` | 实验部署前是否做代码评审 |
| `illustration` | `false` | 是否生成方法图或架构图 |
| `reviewer` | `codex` | 使用哪个 reviewer 后端 |

### effort 级别

| 级别 | 适合场景 |
|---|---|
| `lite` | 快速探索，节省 token |
| `balanced` | 默认模式，适合日常使用 |
| `max` | 投稿前认真打磨 |
| `beast` | 需要最强审查和最多轮迭代时使用 |

示例：

```text
/idea-discovery "LLM agent memory" — effort: lite
/paper-writing "NARRATIVE_REPORT.md" — effort: max
/auto-review-loop "paper/" — effort: beast, human checkpoint: true
```

---

## 更新 ARIS

如果你使用项目级 symlink 安装，更新 ARIS 本体即可：

```bash
cd ~/aris
git pull
```

如果上游新增或删除了技能，需要在项目目录重新 reconcile：

```bash
cd ~/your-research-project
bash ~/aris/tools/install_aris.sh
```

查看安装计划但不实际执行：

```bash
bash ~/aris/tools/install_aris.sh --dry-run
```

---

## 卸载

在项目目录中执行：

```bash
bash ~/aris/tools/install_aris.sh --uninstall
```

卸载只会删除 ARIS 管理的技能链接，不会删除你的项目文件，也不会删除你自己的 `.claude/skills` 内容。

---

## 可选集成

### Codex Reviewer

用于外部评审、论文审稿、代码审查等：

```bash
npm install -g @openai/codex
codex setup
claude mcp add codex -s user -- codex mcp-server
```

### Zotero

如果你用 Zotero 管理文献，可以接入 Zotero MCP，让 `/research-lit` 优先搜索你的个人文献库。

典型能力：

- 搜索本地文献
- 读取 annotation
- 导出 BibTeX
- 结合 web search 做补充调研

### Obsidian

如果你用 Obsidian 管理研究笔记，可以接入 vault 搜索，让 ARIS 在生成 idea 前读取你的历史笔记。

### Feishu / Lark 通知

如果你希望长时间任务完成后收到通知，可以配置 Feishu webhook。  
这是可选功能，不配置不会影响任何核心工作流。

### Vast.ai

如果没有固定 GPU，可以配置 Vast.ai，按需租用 GPU 执行实验。  
建议先手动熟悉 Vast.ai 的计费和实例销毁逻辑，再让 agent 自动操作。

---

## 目录结构

仓库主要目录：

```text
.
├── skills/                  # Claude Code / Codex 技能
├── tools/                   # 安装、更新、辅助脚本
├── templates/               # 输入模板
├── docs/                    # 详细文档
├── mcp-servers/             # 可选 MCP server
├── community_papers/        # 社区示例材料
└── README.md
```

常用文件：

| 文件或目录 | 说明 |
|---|---|
| `skills/*/SKILL.md` | 每个技能的主说明 |
| `tools/install_aris.sh` | 项目级安装脚本 |
| `tools/smart_update.sh` | 复制式安装时的更新工具 |
| `templates/` | research brief、experiment plan、paper plan 等模板 |
| `docs/` | 平台适配和进阶配置文档 |

---

## 输入模板

建议从模板开始，而不是只给一句很宽泛的 prompt。

常见模板：

```text
templates/
  RESEARCH_BRIEF_TEMPLATE.md
  EXPERIMENT_PLAN_TEMPLATE.md
  NARRATIVE_REPORT_TEMPLATE.md
  PAPER_PLAN_TEMPLATE.md
```

典型使用方式：

```bash
cp ~/aris/templates/RESEARCH_BRIEF_TEMPLATE.md RESEARCH_BRIEF.md
cp ~/aris/templates/NARRATIVE_REPORT_TEMPLATE.md NARRATIVE_REPORT.md
```

然后在 Claude Code 中运行：

```text
/idea-discovery "read RESEARCH_BRIEF.md"
/paper-writing "NARRATIVE_REPORT.md"
```

---

## 最佳实践

### 1. 把研究上下文写进文件

不要只依赖聊天上下文。建议维护：

```text
RESEARCH_BRIEF.md
EXPERIMENT_PLAN.md
NARRATIVE_REPORT.md
EXPERIMENT_LOG.md
CLAIMS.md
```

这样 agent 断线、重启或压缩上下文后仍能恢复。

### 2. 每个实验都保存配置和结果

建议统一结构：

```text
runs/
  2026-05-18_baseline/
    config.yaml
    metrics.jsonl
    stdout.log
    summary.md
  2026-05-18_method_a/
    config.yaml
    metrics.jsonl
    stdout.log
    summary.md
```

### 3. 不要让 agent 编造结果

论文写作前，确保所有 claim 都能对应到实际结果文件。  
如果没有数据，让 agent 标记为 `DATA_NEEDED`，不要生成看似真实的数字。

### 4. 投稿前人工检查

ARIS 可以提高效率，但不能替代作者判断。投稿前至少人工检查：

- 数字是否来自真实实验
- 引用是否真实且上下文正确
- 代码是否能复现关键结果
- 图表是否和结果文件一致
- 贡献表述是否过度
- limitation 是否诚实

### 5. 高风险操作前加 human checkpoint

例如：

```text
/research-pipeline "topic" — human checkpoint: true
/auto-review-loop "paper/" — human checkpoint: true
```

适合在以下节点人工确认：

- 选择最终 idea
- 启动大规模 GPU 实验
- 修改论文核心 claim
- 生成 rebuttal 承诺
- 推送代码或覆盖文件

---

## 常见问题

### Claude Code 里看不到 slash command 怎么办？

确认技能是否安装到项目的：

```text
.claude/skills/<skill-name>/SKILL.md
```

如果你之前使用了旧的嵌套安装方式，重新运行：

```bash
bash ~/aris/tools/install_aris.sh --from-old
```

### `git pull` 后新技能没出现怎么办？

项目级安装采用一个技能一个 symlink。  
新增技能需要重新运行安装脚本：

```bash
cd ~/your-research-project
bash ~/aris/tools/install_aris.sh
```

### 不装 Codex 能不能用？

可以使用部分功能，例如读写文件、整理文档、生成计划。  
但自动评审、外部审稿、对抗性检查等能力会受限。

推荐至少配置一个 reviewer 后端，例如 Codex MCP 或其他 OpenAI-compatible MCP。

### 不写论文，只做实验可以吗？

可以。常用组合是：

```text
/idea-discovery
/experiment-bridge
/run-experiment
/monitor-experiment
/analyze-results
```

### 没有 GPU 可以用吗？

可以。  
没有 GPU 时，ARIS 仍然能做文献调研、idea 生成、代码修改、论文写作和 rebuttal。  
涉及实验运行的步骤会跳过、请求远程 GPU，或使用你配置的云 GPU。

### 生成的论文能直接投稿吗？

不建议不经人工检查直接投稿。  
ARIS 可以生成结构化草稿和做多轮审查，但作者仍然需要负责真实性、贡献边界、实验有效性和引用准确性。

---

## 贡献

欢迎提交：

- 新技能
- 现有技能改进
- 文档修正
- 平台适配
- bug report
- 使用案例

新增技能通常放在：

```text
skills/your-skill-name/SKILL.md
```

建议每个技能说明清楚：

- 适用场景
- 输入格式
- 输出文件
- 执行步骤
- 安全限制
- 失败时如何恢复

---

## Citation

如果你在研究中使用 ARIS，可以引用：

```bibtex
@article{yang2026aris,
  title={ARIS: Autonomous Research via Adversarial Multi-Agent Collaboration},
  author={Yang, Ruofeng and Li, Yongcan and Li, Shuai},
  journal={arXiv preprint arXiv:2605.03042},
  year={2026}
}
```

---

## License

MIT
