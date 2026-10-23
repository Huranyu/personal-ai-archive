# personal-ai-archive

> 全域设备通用的个人 AI 记忆系统 —— 一次搭建、全平台复用。

## 这是什么

一个公开的 GitHub 仓库，用标准化 Markdown 存放我的身份、工作习惯、历史经验、技能与归档。任何 AI 平台（ChatGPT / Gemini / Claude / 各类 Agent）都可以通过 GitHub API 或 git 直接读取，实现跨设备、跨平台的个人档案复用。

## 目录结构

```
personal-ai-archive/
├── README.md               # 本文件
├── ai-integration.md       # 跨 AI 平台接入配置（先读这个）
├── identity/               # 个人身份核心信息（AI 只读）
│   └── profile.md
├── workflow/               # 工作习惯、工具、沟通风格、优先级规则
│   └── workflow.md
├── memory/                 # 历史经验、决策记录、AI 偏好沉淀
│   ├── ai-preferences.md
│   ├── decisions.md
│   └── experience.md
├── skills/                 # 技能清单、学习计划
│   └── skills.md
└── archive/                # 历史项目与任务归档
    └── archive.md
```

## 快速上手

1. 填写 `identity/profile.md` 中的 `{{占位符}}`，确立档案核心
2. 阅读 `ai-integration.md`，按第 2 节把你的 AI 平台接进来
3. 日常使用中，AI 会按第 4 节规则自动沉淀新经验到 `memory/`
4. 任何误操作都可用 `git revert` 一键回退

## 版本控制

main 分支已开启保护（禁止 force push / 删除），所有修改均有提交记录可追溯。
