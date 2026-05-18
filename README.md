
# ARIS

ARIS 是一组可安装到 Claude Code / Codex / Cursor 等 agent 工具里的科研工作流技能。

常见用法：

```text
/idea-discovery "test-time scaling for LLM agents"
/experiment-bridge "EXPERIMENT_PLAN.md"
/auto-review-loop "paper/"
/paper-writing "NARRATIVE_REPORT.md"
/rebuttal "paper/ + reviews.md" — venue: ICML, character limit: 5000
```

---

## 1. 安装

### 1.1 克隆仓库

```bash
git clone https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep.git ~/aris
```

### 1.2 给当前项目安装 ARIS skills

```bash
cd ~/your-project
bash ~/aris/tools/install_aris.sh
```

安装后应该能看到：

```bash
ls .claude/skills
```

示例输出：

```text
idea-discovery
experiment-bridge
auto-review-loop
paper-writing
rebuttal
research-lit
paper-compile
...
```

### 1.3 启动 Claude Code

```bash
claude
```

测试：

```text
/research-lit "diffusion language models"
```

---

## 2. 最小可用配置

在项目根目录新建或修改 `CLAUDE.md`：

````markdown
# Project Config

## Environment

Use Python 3.10.

Activate env:

```bash
conda activate research
```

## Code

Main code is under:

```text
src/
```

Experiment configs are under:

```text
configs/
```

Save runs to:

```text
runs/
```

## Paper

Paper source is under:

```text
paper/
```

Compile with:

```bash
cd paper && latexmk -pdf main.tex
```
````

---

## 3. Reviewer 配置

ARIS 的 review 类命令通常需要一个 reviewer 后端。推荐用 Codex MCP。

### 3.1 默认 Codex / OpenAI 配置

```bash
npm install -g @openai/codex
codex setup
claude mcp add codex -s user -- codex mcp-server
```

测试：

```bash
codex exec "say hello"
```

Claude Code 内测试：

```text
/research-review "README.md"
```

---

## 4. 阿里云百炼 OpenAI 兼容接口

如果用阿里云百炼的 OpenAI 兼容接口作为 reviewer，例如 `glm-5`，建议用旧版 Codex 的 Chat/Completions API。

百炼控制台：

```text
https://bailian.console.aliyun.com/
```

### 4.1 安装旧版 Codex

```bash
npm install -g @openai/codex@0.80.0
```

检查：

```bash
codex --version
```

### 4.2 配置 API Key

```bash
export OPENAI_API_KEY="your-bailian-api-key"
```

写入 shell 配置：

```bash
echo 'export OPENAI_API_KEY="your-bailian-api-key"' >> ~/.bashrc
source ~/.bashrc
```

或者 zsh：

```bash
echo 'export OPENAI_API_KEY="your-bailian-api-key"' >> ~/.zshrc
source ~/.zshrc
```

### 4.3 配置 `~/.codex/config.toml`

```bash
mkdir -p ~/.codex
vim ~/.codex/config.toml
```

写入：

```toml
model_provider = "Model_Studio_Token_Plan"
model = "glm-5"

[model_providers.Model_Studio_Token_Plan]
name = "Model_Studio_Token_Plan"
base_url = "https://token-plan.cn-beijing.maas.aliyuncs.com/compatible-mode/v1"
env_key = "OPENAI_API_KEY"
wire_api = "chat"
```

测试：

```bash
codex exec "用一句话说明你是什么模型"
```

接入 Claude Code：

```bash
claude mcp add codex -s user -- codex mcp-server
```

Claude Code 内测试：

```text
/research-review "README.md"
```

### 4.4 禁止 Codex 自动更新

如果你的模型依赖：

```toml
wire_api = "chat"
```

建议锁住 Codex 版本，避免自动更新后不能用 Chat/Completions API。

先查看 npm 全局目录：

```bash
npm root -g
```

如果路径是：

```text
/home/tiger/miniforge3/lib/node_modules
```

锁住 Codex：

```bash
sudo chown -R root:root /home/tiger/miniforge3/lib/node_modules/@openai/codex
```

通用写法：

```bash
sudo chown -R root:root "$(npm root -g)/@openai/codex"
```

确认还能执行：

```bash
codex --version
codex exec "hello"
```

如果以后要更新 Codex：

```bash
sudo chown -R "$USER":"$USER" "$(npm root -g)/@openai/codex"
npm install -g @openai/codex@0.80.0
```

---

## 5. 常用命令

### 5.1 查文献

```text
/research-lit "diffusion language models"
/research-lit "test-time scaling LLM agents"
/research-lit "multi-agent reinforcement learning for tool use"
/research-lit "LLM reasoning verification" — sources: local, web
/research-lit "agent memory benchmark" — sources: web
```

如果你有本地论文目录：

```bash
mkdir -p papers
cp ~/Downloads/*.pdf papers/
```

然后：

```text
/research-lit "read local papers about reward model overoptimization" — sources: local
```

### 5.2 生成研究想法

```text
/idea-creator "efficient test-time scaling for language model agents"
/idea-creator "post-training methods for small reasoning models"
/idea-creator "how to improve long-horizon tool-use agents"
```

更具体一点：

```text
/idea-creator "I have a repo that trains ReAct agents on web tasks. Find low-cost ideas that can be tested in 1 day on 1 GPU."
```

### 5.3 从方向到 idea report

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

### 5.4 检查 novelty

```text
/novelty-check "Use verifier disagreement as an exploration bonus for test-time search"
/novelty-check "Train a memory compressor by preserving future tool-call success"
```

带上下文：

```text
/novelty-check "Our idea is in IDEA.md. Check whether it is already done in recent LLM agent papers."
```

### 5.5 写实验计划

```text
/experiment-plan "IDEA.md"
/experiment-plan "We want to test whether memory compression preserves task success on WebArena."
```

建议保存成：

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

### 5.6 实现并启动实验

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

只做代码，不自动跑：

```text
/experiment-bridge "EXPERIMENT_PLAN.md" — auto deploy: false
```

跳过 reviewer 代码审查：

```text
/experiment-bridge "EXPERIMENT_PLAN.md" — code review: false
```

### 5.7 单独跑实验

```text
/run-experiment train.py --config configs/baseline.yaml --seed 1
/run-experiment train.py --config configs/method.yaml --seed 1
/run-experiment "python train.py --config configs/baseline.yaml --seed 1"
```

### 5.8 监控实验

```text
/monitor-experiment
/monitor-experiment "runs/2026-05-18_baseline"
/monitor-experiment "check all running screen sessions on remote GPU"
```

### 5.9 分析结果

```text
/analyze-results "runs/"
/analyze-results "runs/baseline runs/method"
/analyze-results "Compare method vs baseline on success rate and cost."
```

---

## 6. Auto Review

### 6.1 审论文

```text
/research-review "paper/"
/research-review "paper/main.pdf"
/research-review "NARRATIVE_REPORT.md"
```

### 6.2 审实验报告

```text
/research-review "EXPERIMENT_LOG.md"
/research-review "runs/summary.md"
```

### 6.3 多轮自动修改

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

## 7. 写论文

### 7.1 准备 `NARRATIVE_REPORT.md`

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

### 7.2 一条命令生成论文

```text
/paper-writing "NARRATIVE_REPORT.md"
```

### 7.3 分步骤生成

```text
/paper-plan "NARRATIVE_REPORT.md"
/paper-figure "NARRATIVE_REPORT.md"
/paper-write "PAPER_PLAN.md"
/paper-compile "paper/"
```

### 7.4 指定会议

```text
/paper-writing "NARRATIVE_REPORT.md" — venue: ICLR
/paper-writing "NARRATIVE_REPORT.md" — venue: NeurIPS
/paper-writing "NARRATIVE_REPORT.md" — venue: ICML
/paper-writing "NARRATIVE_REPORT.md" — venue: ACL
/paper-writing "NARRATIVE_REPORT.md" — venue: CVPR
```

### 7.5 不生成 AI 图

```text
/paper-writing "NARRATIVE_REPORT.md" — illustration: false
```

### 7.6 使用 Mermaid 图

```text
/paper-writing "NARRATIVE_REPORT.md" — illustration: mermaid
```

### 7.7 编译论文

```bash
cd paper
latexmk -pdf main.tex
```

或者：

```text
/paper-compile "paper/"
```

---

## 8. Rebuttal

### 8.1 准备 reviews

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

### 8.2 生成 rebuttal

```text
/rebuttal "paper/ + reviews.md" — venue: ICML, character limit: 5000
```

### 8.3 只生成策略，不写最终稿

```text
/rebuttal "paper/ + reviews.md" — venue: ICML, character limit: 5000, quick mode: true
```

### 8.4 允许补实验

```text
/rebuttal "paper/ + reviews.md" — venue: ICML, character limit: 5000, auto experiment: true
```

输出文件一般是：

```text
PASTE_READY.txt
REBUTTAL_DRAFT_rich.md
```

---

## 9. Research Wiki

### 9.1 初始化

```text
/research-wiki init
```

生成：

```text
research-wiki/
```

### 9.2 查询

```text
/research-wiki query "agent memory"
/research-wiki query "failed ideas about verifier"
/research-wiki stats
```

### 9.3 手动记录 idea

```text
/research-wiki update idea:001 — outcome: failed, reason: "OOM when context length > 64k"
```

---

## 10. 远程 GPU 配置

在 `CLAUDE.md` 写：

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

测试 SSH：

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

## 11. 本地 GPU 配置

在 `CLAUDE.md` 写：

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
/run-experiment "python train.py --config configs/baseline.yaml"
/monitor-experiment
```

---

## 12. Vast.ai 配置

安装：

```bash
pip install vastai
```

配置 API key：

```bash
vastai set api-key YOUR_API_KEY
```

测试：

```bash
vastai search offers 'gpu_ram>=24 reliability>0.95' -o 'dph+' --limit 3
```

`CLAUDE.md`：

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

## 13. 常用参数

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

## 14. 更新

更新 ARIS：

```bash
cd ~/aris
git pull
```

如果新技能没有出现在项目里：

```bash
cd ~/your-project
bash ~/aris/tools/install_aris.sh
```

查看会改什么：

```bash
bash ~/aris/tools/install_aris.sh --dry-run
```

---

## 15. 卸载

```bash
cd ~/your-project
bash ~/aris/tools/install_aris.sh --uninstall
```

确认：

```bash
ls .claude/skills
```

---

## 16. 常见问题

### Claude Code 看不到命令

检查：

```bash
ls .claude/skills
find .claude/skills -maxdepth 2 -name SKILL.md | head
```

重新安装：

```bash
bash ~/aris/tools/install_aris.sh
```

旧安装迁移：

```bash
bash ~/aris/tools/install_aris.sh --from-old
```

### Codex 不通

检查版本：

```bash
codex --version
```

检查 API key：

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

如果新版不兼容：

```bash
npm install -g @openai/codex@0.80.0
```

### 防止 Codex 被更新

```bash
sudo chown -R root:root "$(npm root -g)/@openai/codex"
```

验证：

```bash
codex --version
codex exec "hello"
```

### LaTeX 编译失败

手动看错误：

```bash
cd paper
latexmk -pdf main.tex
```

让 agent 修：

```text
/paper-compile "paper/"
```

### 实验跑挂了

```text
/monitor-experiment "runs/"
/analyze-results "runs/"
```

让 agent 看日志：

```text
/experiment-bridge "Read the failed logs under runs/ and patch the training script."
```

---

## 17. 推荐项目文件结构

```text
your-project/
  CLAUDE.md
  RESEARCH_BRIEF.md
  IDEA.md
  EXPERIMENT_PLAN.md
  NARRATIVE_REPORT.md
  reviews.md

  src/
  configs/
  scripts/

  runs/
    baseline/
    method/
    ablation/

  paper/
    main.tex
    sections/
    figures/
    references.bib

  .claude/
    skills/

  .aris/
```
