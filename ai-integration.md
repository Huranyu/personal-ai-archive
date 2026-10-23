# AI-INTEGRATION: 跨 AI 平台接入配置

> 本文件说明如何让 ChatGPT / Gemini / Claude / AI Agent 等平台读取并更新本仓库。
> 仓库地址：`https://github.com/Huranyu/personal-ai-archive`

## 0. 目录地图（AI 首先阅读本表）

| 目录 | 内容 | 读取时机 | 写入规则 |
| --- | --- | --- | --- |
| `identity/profile.md` | 身份、职业、标签、目标、决策原则 | 每次会话开始 | **只读**，仅本人可改 |
| `workflow/workflow.md` | 工作习惯、工具、沟通风格、优先级 | 每次会话开始 | AI 可追加，需在"偏好变更记录"留痕 |
| `memory/ai-preferences.md` | AI 对话沉淀的偏好 | 每次会话开始 | AI 可追加 |
| `memory/decisions.md` | 重要决策记录 | 涉及决策类任务前 | AI 可追加，禁止修改历史条目 |
| `memory/experience.md` | 历史经验与教训 | 遇到相似任务时 | AI 可追加 |
| `skills/skills.md` | 技能清单、学习计划 | 涉及能力匹配时 | AI 可更新熟练度 |
| `archive/archive.md` | 历史归档 | 需要追溯时 | 只增不改 |

## 1. 通过 GitHub API 读取（ChatGPT / Gemini / Claude 通用）

任何支持联网或代码执行的大模型，按以下方式读取：

```bash
# 读取单文件（以身份档案为例）
curl -s https://raw.githubusercontent.com/Huranyu/personal-ai-archive/main/identity/profile.md

# 读取目录清单
curl -s https://api.github.com/repos/Huranyu/personal-ai-archive/contents/

# 一次性拉取全仓库（AI Agent 适用）
curl -sL https://github.com/Huranyu/personal-ai-archive/archive/refs/heads/main.tar.gz -o archive.tar.gz && tar -xzf archive.tar.gz
```

公开仓库无需鉴权即可读；若仓库转为私有，需在请求头带 PAT：
`-H "Authorization: Bearer <PAT>"`。

## 2. 各平台接入方式

| 平台 | 接入方式 | 操作步骤 |
| --- | --- | --- |
| ChatGPT | 自定义 GPT + Actions | New GPT → Configure → Actions → 添加 GET 动作指向上述 raw URL；或在对话中直接贴文件内容 |
| Gemini | 公开链接 + Deep Research | 对话中提供 `raw.githubusercontent.com/...` 链接，Gemini 可直接抓取 |
| Claude | Projects / MCP GitHub | Projects → Add content 贴入文件；或配置 GitHub MCP server 读写仓库 |
| 本地 Agent（Meta Muse / OpenAI Dots 等） | Git 同步 | `git clone https://github.com/Huranyu/personal-ai-archive.git`，按第 4 节规则同步 |

## 3. 写入方式（AI 更新档案）

推荐通过 GitHub API 提交，保留版本记录：

```bash
# 获取文件当前 SHA（更新必需）
curl -s -H "Authorization: Bearer <PAT>" \
  https://api.github.com/repos/Huranyu/personal-ai-archive/contents/memory/ai-preferences.md \
  | jq -r '.sha'

# 更新文件（base64 编码新内容）
curl -X PUT -H "Authorization: Bearer <PAT>" \
  https://api.github.com/repos/Huranyu/personal-ai-archive/contents/memory/ai-preferences.md \
  -d '{
    "message": "feat(memory): AI 追加新偏好 - <简述>",
    "content": "<base64(新文件内容)>",
    "sha": "<上一步获取的SHA>"
  }'
```

Git 方式（本地 Agent）：

```bash
git pull --rebase
# 编辑对应文件
git add . && git commit -m "feat(memory): <变更说明>"
git push
```

## 4. 自动更新规则（AI 必须遵守）

1. **任务完成后**，AI 判断本次交互是否产生新经验/偏好/决策：
   - 有新偏好 → 追加到 `memory/ai-preferences.md`，并在"偏好变更记录"表格加一行
   - 有重要决策 → 追加到 `memory/decisions.md`
   - 有可复用经验 → 追加到 `memory/experience.md`
   - 有已完成交付 → 追加到 `archive/archive.md`
2. **提交信息规范**：`类型(目录): 说明`，类型限 `feat / fix / chore`
3. **禁止操作**：删除历史条目、修改 `identity/`、重写他人提交（force push 已被分支保护拦截）
4. **冲突处理**：push 前 `git pull --rebase`；API 写入前必须重新获取 SHA

## 5. 安全与版本控制

- 仓库已开启 **main 分支保护**：禁止 force push、禁止删除分支，所有修改必须走提交记录
- 每次修改自动生成 commit，可随时回退：
  ```bash
  git log --oneline          # 查看历史
  git revert <commit-sha>    # 一键回退任意版本
  ```
- 敏感信息（Token、密码、证件号）**禁止**写入本仓库
- Token 仅保存在本人密码管理器中，不落盘、不进 Git

## 6. 快速自检清单（AI 接入前确认）

- [ ] 能 `curl` 打开 `identity/profile.md`
- [ ] 能读取目录地图并复述五大目录用途
- [ ] 已阅读第 4 节自动更新规则
- [ ] 提交前会执行 `git pull --rebase` 或重新获取 SHA
