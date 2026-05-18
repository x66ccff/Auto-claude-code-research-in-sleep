
# ARIS

ARIS 是一组可安装到 Claude Code / Codex / Cursor 等 agent 工具里的科研工作流 skills。

```text
/idea-discovery "test-time scaling for LLM agents"
/experiment-bridge "EXPERIMENT_PLAN.md"
/auto-review-loop "paper/"
/paper-writing "NARRATIVE_REPORT.md"
/rebuttal "paper/ + reviews.md" — venue: ICML, character limit: 5000
```

---

## 安装

```bash
git clone https://github.com/x66ccff/Auto-claude-code-research-in-sleep.git

mkdir -p ~/.claude/skills/
cp -r Auto-claude-code-research-in-sleep/skills/* ~/.claude/skills/
```

安装 Codex reviewer：

```bash
npm install -g @openai/codex@0.80.0
codex setup
claude mcp add codex -s user -- codex mcp-server
```

启动 Claude Code：

```bash
claude
```

测试：

```text
/research-lit "diffusion language models"
```

---

## Quick Start

```text
/idea-discovery "your research direction"
/experiment-bridge
/auto-review-loop "your paper topic or scope"
/paper-writing "NARRATIVE_REPORT.md"
/rebuttal "paper/ + reviews" — venue: ICML
/resubmit-pipeline "paper/" — venue: NeurIPS
/paper-talk "paper/" — venue: ICLR
/research-pipeline "your research direction"
/research-wiki init
/meta-optimize
```

更具体的例子：

```text
/idea-discovery "factorized gap in discrete diffusion LMs"
/experiment-bridge "EXPERIMENT_PLAN.md"
/auto-review-loop "focus on novelty, experimental validity, and overclaiming"
/paper-writing "NARRATIVE_REPORT.md" — venue: NeurIPS
/rebuttal "paper/ + reviews.md" — venue: ICML, character limit: 5000
```

---

如果使用百炼 OpenAI 兼容接口作为 reviewer，例如 `glm-5`，建议使用 Codex `0.80.0`，因为它支持 Chat/Completions API。

### 安装旧版 Codex

```bash
npm install -g @openai/codex@0.80.0
codex --version
```


### 禁止 Codex 自动更新

锁住 Codex npm 包目录：

```bash
sudo chown -R root:root "$(npm root -g)/@openai/codex"
```

确认还能执行：

```bash
codex --version
codex exec "hello"
```

如果你的 npm 全局目录是：

```text
/home/tiger/miniforge3/lib/node_modules
```

也可以直接写：

```bash
sudo chown -R root:root /home/tiger/miniforge3/lib/node_modules/@openai/codex
```

以后需要更新时再改回来：

```bash
sudo chown -R "$USER":"$USER" "$(npm root -g)/@openai/codex"
npm install -g @openai/codex@0.80.0
```

### 全换成glm5.1

```bash
#!/bin/bash

# 脚本：将 skills 文件夹中带后缀的模型名称统一替换为 glm-5.1
# 处理 glm-5.1 和 gpt-5.x-xxx 格式的模型名称

SKILLS_DIR="/home/tiger/.claude/skills"

echo "开始修复带后缀的模型名称..."
echo ""

# 查找所有包含 glm-5.1 或 gpt-5.x-xxx 的文件并进行替换
find "$SKILLS_DIR" -type f \( -name "*.md" -o -name "*.py" -o -name "*.json" -o -name "*.yaml" -o -name "*.yml" -o -name "*.sh" -o -name "*.txt" \) | while read -r file; do
    # 检查文件是否包含需要替换的模式
    if grep -qE '(glm-5\.1-[a-zA-Z0-9-]+|gpt-5\.[0-9]+-[a-zA-Z0-9-]+)' "$file" 2>/dev/null; then
        echo "处理文件: $file"
        # 使用 sed 替换带后缀的模型名称为 glm-5.1
        sed -i -E 's/(glm-5\.1-[a-zA-Z0-9-]+|gpt-5\.[0-9]+-[a-zA-Z0-9-]+)/glm-5.1/g' "$file"
    fi
done

echo ""
echo "修复完成！"
echo ""
echo "验证结果（搜索剩余的带后缀模型名称）:"
remaining=$(grep -rE '(glm-5\.1-[a-zA-Z0-9-]+|gpt-5\.[0-9]+-[a-zA-Z0-9-]+)' "$SKILLS_DIR" 2>/dev/null | wc -l)
if [ "$remaining" -eq 0 ]; then
    echo "✓ 所有带后缀的模型名称已统一为 glm-5.1"
else
    echo "✗ 还有 $remaining 处带后缀的模型名称:"
    grep -rE '(glm-5\.1-[a-zA-Z0-9-]+|gpt-5\.[0-9]+-[a-zA-Z0-9-]+)' "$SKILLS_DIR" 2>/dev/null
fi


```

### 在任何仓库下初始化codex（安装后可输入initcodex）

```bash
mkdir -p ~/bin
cat > ~/bin/initcodex <<'SCRIPT'
#!/usr/bin/env bash
set -euo pipefail
TARGET_DIR="${1:-$(pwd)}"
CODEX_DIR="${TARGET_DIR}/.codex"
CONFIG_FILE="${CODEX_DIR}/config.toml"
mkdir -p "${CODEX_DIR}"
if [[ -f "${CONFIG_FILE}" ]]; then
    BACKUP="${CONFIG_FILE}.bak.$(date +%Y%m%d%H%M%S)"
    cp "${CONFIG_FILE}" "${BACKUP}"
    echo "[initcodex] 已备份旧文件至: ${BACKUP}"
fi
cat > "${CONFIG_FILE}" <<'EOF'
model_provider = "Model_Studio_Token_Plan"
# model = "kimi-k2.6"
model = "glm-5.1"
[model_providers.Model_Studio_Token_Plan]
name = "Model_Studio_Token_Plan"
base_url = "https://token-plan.cn-beijing.maas.aliyuncs.com/compatible-mode/v1"
env_key = "OPENAI_API_KEY"
wire_api = "chat"
EOF
echo "[initcodex] 已创建: ${CONFIG_FILE}"
SCRIPT
chmod +x ~/bin/initcodex
# 若 ~/bin 不在 PATH,追加一次即可
grep -q 'HOME/bin' ~/.bashrc || echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

---

## 查文献

```text
/research-lit "diffusion language models"
/research-lit "test-time scaling LLM agents"
/research-lit "multi-agent reinforcement learning for tool use"
/research-lit "LLM reasoning verification"
/research-lit "agent memory benchmark"
```

指定来源：

```text
/research-lit "LLM reasoning verification" — sources: local, web
/research-lit "agent memory benchmark" — sources: web
/research-lit "reward model overoptimization" — sources: local
```

本地论文目录：

```bash
mkdir -p papers
cp ~/Downloads/*.pdf papers/
```

然后：

```text
/research-lit "read local papers about reward model overoptimization" — sources: local
```

---

## 生成研究想法

```text
/idea-creator "efficient test-time scaling for language model agents"
/idea-creator "post-training methods for small reasoning models"
/idea-creator "how to improve long-horizon tool-use agents"
```

更具体：

```text
/idea-creator "I have a repo that trains ReAct agents on web tasks. Find low-cost ideas that can be tested in 1 day on 1 GPU."
```

从方向直接生成 idea report：

```text
/idea-discovery "LLM agents that learn from failed tool calls"
/idea-discovery "efficient verifier-guided reasoning for math LLMs"
/idea-discovery "memory compression for long-horizon agents"
```

带参数：

```text
/idea-discovery "agent memory compression" — effort: lite
/idea-discovery "agent memory compression" — effort: max
/idea-discovery "agent memory compression" — sources: local, web
/idea-discovery "agent memory compression" — human checkpoint: true
```

---

## 检查 novelty

```text
/novelty-check "Use verifier disagreement as an exploration bonus for test-time search"
/novelty-check "Train a memory compressor by preserving future tool-call success"
```

带上下文：

```text
/novelty-check "Our idea is in IDEA.md. Check whether it is already done in recent LLM agent papers."
```

---

## 写实验计划

```text
/experiment-plan "IDEA.md"
/experiment-plan "We want to test whether memory compression preserves task success on WebArena."
```

新建计划文件：

```bash
touch EXPERIMENT_PLAN.md
```

最小模板：

```markdown
# Experiment Plan

## Claim

The proposed method improves task success under the same context budget.

## Baseline

Use the current agent without memory compression.

## Method

Add a memory compressor before each long-horizon planning step.

## Metrics

- task success rate
- average context length
- average tool calls
- cost per successful task

## Runs

| name | config | seeds |
|---|---|---|
| baseline | configs/baseline.yaml | 3 |
| method | configs/memory_compress.yaml | 3 |
| ablation_no_verifier | configs/no_verifier.yaml | 3 |
```

然后：

```text
/experiment-bridge "EXPERIMENT_PLAN.md"
```

---

## 实现并启动实验

```text
/experiment-bridge "EXPERIMENT_PLAN.md"
/experiment-bridge "Implement the runs in EXPERIMENT_PLAN.md using the existing training code."
```

本地 GPU：

```text
/experiment-bridge "EXPERIMENT_PLAN.md" — gpu: local
```

远程 GPU：

```text
/experiment-bridge "EXPERIMENT_PLAN.md" — gpu: remote
```

只写代码，不自动跑：

```text
/experiment-bridge "EXPERIMENT_PLAN.md" — auto deploy: false
```

跳过 reviewer 代码审查：

```text
/experiment-bridge "EXPERIMENT_PLAN.md" — code review: false
```

---

## 单独跑实验

```text
/run-experiment train.py --config configs/baseline.yaml --seed 1
/run-experiment train.py --config configs/method.yaml --seed 1
/run-experiment "python train.py --config configs/baseline.yaml --seed 1"
```

远程跑：

```text
/run-experiment "python train.py --config configs/baseline.yaml --seed 1" — gpu: remote
```

本地跑：

```text
/run-experiment "python train.py --config configs/baseline.yaml --seed 1" — gpu: local
```

---

## 监控实验

```text
/monitor-experiment
/monitor-experiment "runs/2026-05-18_baseline"
/monitor-experiment "check all running screen sessions on remote GPU"
```

---

## 分析结果

```text
/analyze-results "runs/"
/analyze-results "runs/baseline runs/method"
/analyze-results "Compare method vs baseline on success rate and cost."
```

---

## Auto Review

审论文：

```text
/research-review "paper/"
/research-review "paper/main.pdf"
/research-review "NARRATIVE_REPORT.md"
```

审实验报告：

```text
/research-review "EXPERIMENT_LOG.md"
/research-review "runs/summary.md"
```

多轮自动修改：

```text
/auto-review-loop "paper/"
/auto-review-loop "NARRATIVE_REPORT.md"
/auto-review-loop "Focus on novelty, experimental validity, and overclaiming."
```

带人工确认：

```text
/auto-review-loop "paper/" — human checkpoint: true
```

控制强度：

```text
/auto-review-loop "paper/" — effort: lite
/auto-review-loop "paper/" — effort: max
/auto-review-loop "paper/" — effort: beast
```

限制轮数：

```text
/auto-review-loop "paper/" — max rounds: 2
```

---

## 写论文

准备 `NARRATIVE_REPORT.md`：

```bash
cat > NARRATIVE_REPORT.md <<'EOF'
# Narrative Report

## Problem

Current LLM agents lose useful information when the trajectory becomes long.

## Method

We add a memory compressor that keeps information needed for future tool calls.

## Claims

### C1

The method reduces context length without hurting task success.

### C2

Verifier-guided compression is better than summary-only compression.

## Evidence

- Baseline results: runs/baseline/summary.json
- Method results: runs/method/summary.json
- Ablation results: runs/ablation_no_verifier/summary.json

## Figures

- Figure 1: method overview
- Figure 2: success rate vs context length
- Table 1: main results
- Table 2: ablations

## Limitations

Tested on one benchmark only.
No human evaluation yet.
EOF
```

一条命令生成论文：

```text
/paper-writing "NARRATIVE_REPORT.md"
```

分步骤生成：

```text
/paper-plan "NARRATIVE_REPORT.md"
/paper-figure "NARRATIVE_REPORT.md"
/paper-write "PAPER_PLAN.md"
/paper-compile "paper/"
```

指定会议：

```text
/paper-writing "NARRATIVE_REPORT.md" — venue: ICLR
/paper-writing "NARRATIVE_REPORT.md" — venue: NeurIPS
/paper-writing "NARRATIVE_REPORT.md" — venue: ICML
/paper-writing "NARRATIVE_REPORT.md" — venue: ACL
/paper-writing "NARRATIVE_REPORT.md" — venue: CVPR
```

不生成 AI 图：

```text
/paper-writing "NARRATIVE_REPORT.md" — illustration: false
```

使用 Mermaid 图：

```text
/paper-writing "NARRATIVE_REPORT.md" — illustration: mermaid
```

手动编译：

```bash
cd paper
latexmk -pdf main.tex
```

或者：

```text
/paper-compile "paper/"
```

---

## Rebuttal

准备 reviews：

```bash
cat > reviews.md <<'EOF'
# Review 1

The idea is interesting but the experimental comparison is weak.

# Review 2

The method is not clearly distinguished from prior memory summarization methods.

# Review 3

The paper lacks ablations on the verifier component.
EOF
```

生成 rebuttal：

```text
/rebuttal "paper/ + reviews.md" — venue: ICML, character limit: 5000
```

只生成策略，不写最终稿：

```text
/rebuttal "paper/ + reviews.md" — venue: ICML, character limit: 5000, quick mode: true
```

允许补实验：

```text
/rebuttal "paper/ + reviews.md" — venue: ICML, character limit: 5000, auto experiment: true
```

常见输出：

```text
PASTE_READY.txt
REBUTTAL_DRAFT_rich.md
```

---

## Research Wiki

初始化：

```text
/research-wiki init
```

生成目录：

```text
research-wiki/
```

查询：

```text
/research-wiki query "agent memory"
/research-wiki query "failed ideas about verifier"
/research-wiki stats
```

手动记录 idea：

```text
/research-wiki update idea:001 — outcome: failed, reason: "OOM when context length > 64k"
```

---

## Paper Talk

生成 presentation：

```text
/paper-talk "paper/" — venue: ICLR
/paper-talk "paper/" — venue: NeurIPS
/paper-talk "paper/" — talk type: 15min
```

生成 slides：

```text
/paper-slides "paper/"
```

生成 poster：

```text
/paper-poster "paper/"
```

---

## Resubmit

换 venue，文本迁移：

```text
/resubmit-pipeline "paper/" — venue: NeurIPS
/resubmit-pipeline "paper/" — venue: ICML
/resubmit-pipeline "paper/" — venue: ICLR
```

不允许新实验：

```text
/resubmit-pipeline "paper/" — venue: NeurIPS, no new experiments: true
```

---

## 常用参数

```text
— effort: lite
— effort: balanced
— effort: max
— effort: beast
```

```text
— human checkpoint: true
— code review: false
— auto deploy: false
— gpu: local
— gpu: remote
— gpu: vast
— venue: NeurIPS
— illustration: false
— illustration: mermaid
— sources: local, web
```

示例：

```text
/idea-discovery "agent memory" — effort: lite
/idea-discovery "agent memory" — effort: max, sources: local, web
/experiment-bridge "EXPERIMENT_PLAN.md" — gpu: remote, code review: true
/auto-review-loop "paper/" — effort: beast, human checkpoint: true
/paper-writing "NARRATIVE_REPORT.md" — venue: NeurIPS, illustration: false
```

---

## 推荐项目文件

```text
RESEARCH_BRIEF.md
IDEA.md
EXPERIMENT_PLAN.md
EXPERIMENT_LOG.md
NARRATIVE_REPORT.md
reviews.md
```

推荐目录：

```text
your-project/
  CLAUDE.md
  RESEARCH_BRIEF.md
  IDEA.md
  EXPERIMENT_PLAN.md
  EXPERIMENT_LOG.md
  NARRATIVE_REPORT.md
  reviews.md

  src/
  configs/
  scripts/

  papers/
  runs/
  paper/

  .claude/
    skills/
```

---

## `CLAUDE.md` 示例

```markdown
# Project Config

## Environment

Activate env:

```bash
conda activate research
```

## Code

Main code:

```text
src/
```

Configs:

```text
configs/
```

Scripts:

```text
scripts/
```

## Experiments

Save runs to:

```text
runs/
```

Use this command pattern:

```bash
python train.py --config configs/baseline.yaml --seed 1
```

## Paper

Paper directory:

```text
paper/
```

Compile:

```bash
cd paper && latexmk -pdf main.tex
```
```

---

## 本地 GPU 配置

写到 `CLAUDE.md`：

```markdown
## Local GPU

- gpu: local
- Activate: `conda activate research`
- GPUs: 1x RTX 4090
- Run directory: `runs/`
```

测试：

```bash
nvidia-smi
python -c "import torch; print(torch.cuda.is_available())"
```

运行：

```text
/run-experiment "python train.py --config configs/baseline.yaml" — gpu: local
```

---

## 远程 GPU 配置

写到 `CLAUDE.md`：

```markdown
## Remote GPU

- gpu: remote
- SSH: `ssh my-gpu-server`
- Workdir: `/home/user/projects/my-project`
- Conda env: `research`
- Activate: `conda activate research`
- Launcher: use `screen`
- GPUs: 4x A100
```

测试：

```bash
ssh my-gpu-server "nvidia-smi"
ssh my-gpu-server "cd /home/user/projects/my-project && pwd"
```

运行：

```text
/experiment-bridge "EXPERIMENT_PLAN.md" — gpu: remote
/monitor-experiment "remote GPU"
```

---

## Vast.ai 配置

安装：

```bash
pip install vastai
```

配置：

```bash
vastai set api-key YOUR_API_KEY
```

测试：

```bash
vastai search offers 'gpu_ram>=24 reliability>0.95' -o 'dph+' --limit 3
```

写到 `CLAUDE.md`：

```markdown
## Vast.ai

- gpu: vast
- auto_destroy: true
- max_budget: 5.00
```

运行：

```text
/run-experiment "python train.py --config configs/baseline.yaml" — gpu: vast
```

---

## 更新

更新仓库：

```bash
cd Auto-claude-code-research-in-sleep
git pull
```

重新复制 skills：

```bash
cp -r skills/* ~/.claude/skills/
```

如果使用 `~/aris`：

```bash
cd ~/aris
git pull
cp -r skills/* ~/.claude/skills/
```

---

## 卸载

删除 ARIS skills：

```bash
cd Auto-claude-code-research-in-sleep
ls skills | xargs -I{} rm -rf ~/.claude/skills/{}
```

或者：

```bash
rm -rf ~/.claude/skills/idea-discovery
rm -rf ~/.claude/skills/experiment-bridge
rm -rf ~/.claude/skills/auto-review-loop
rm -rf ~/.claude/skills/paper-writing
rm -rf ~/.claude/skills/rebuttal
```

---

## 常见问题

### Claude Code 看不到命令

检查：

```bash
ls ~/.claude/skills
find ~/.claude/skills -maxdepth 2 -name SKILL.md | head
```

重新安装：

```bash
mkdir -p ~/.claude/skills/
cp -r Auto-claude-code-research-in-sleep/skills/* ~/.claude/skills/
```

重启：

```bash
claude
```

---

### Codex 不通

检查版本：

```bash
codex --version
```

检查 key：

```bash
echo $OPENAI_API_KEY
```

测试：

```bash
codex exec "hello"
```

重新添加 MCP：

```bash
claude mcp add codex -s user -- codex mcp-server
```

---

### 百炼接口不通

检查配置：

```bash
cat ~/.codex/config.toml
```

应包含：

```toml
wire_api = "chat"
```

检查 Codex 版本：

```bash
codex --version
```

重装旧版：

```bash
npm install -g @openai/codex@0.80.0
```

测试：

```bash
codex exec "hello"
```

---

### 防止 Codex 被更新

```bash
sudo chown -R root:root "$(npm root -g)/@openai/codex"
```

验证：

```bash
codex --version
codex exec "hello"
```

---

### LaTeX 编译失败

手动编译看错误：

```bash
cd paper
latexmk -pdf main.tex
```

让 agent 修：

```text
/paper-compile "paper/"
```

---

### 实验跑挂了

看日志：

```text
/monitor-experiment "runs/"
```

分析：

```text
/analyze-results "runs/"
```

让 agent 修：

```text
/experiment-bridge "Read the failed logs under runs/ and patch the training script."
```
