# animal-island-ui-style

让 AI coding agent 按 [animal-island-ui](https://github.com/guokaigdg/animal-island-ui) 的治愈系海岛风格创建或改造界面。

一个可安装的 Skill，适用于 Claude Code、Codex、Cursor 等兼容 `SKILL.md` 的 AI 编程工具。

## 特性

- **两种使用场景**：React + TypeScript 项目（npm 包）或无需构建的单个 HTML 文件
- **设计系统完整约束**：暖色纸张背景、薄荷绿主色、胶囊控件、3D 游戏按钮、软动画
- **组件参考齐全**：31 个组件的 props、合法取值与默认值，随版本同步上游
- **零运行时依赖**：仅参考文档与样式约束，不引入额外代码
- **自动同步**：GitHub Actions 每小时自动拉取上游 Skill 更新

## 快速开始

```bash
# 通过 skills CLI 安装
skills add guokaigdg/animal-island-ui-style

# 或手动复制到 agent 的 skills 目录
# Claude Code: ~/.claude/skills/ 或 <project>/.claude/skills/
cp -R skills/animal-island-ui-style ~/.claude/skills/
```

安装后，向 AI 助手描述需求即可自动套用该风格：

```text
用 animal island 风格帮我做一个任务管理页面
请按 animal-island-ui 的设计系统改造这个登录表单
```

## 使用场景

| 场景 | 入口 |
| --- | --- |
| React 项目中使用 `animal-island-ui` npm 包 | `references/react-project.md` |
| 生成不依赖 npm 和构建工具的单文件 HTML | `references/standalone-html.md` |

## 仓库结构

```text
animal-island-ui-style/
├── README.md
├── LICENSE
└── skills/
    └── animal-island-ui-style/
        ├── SKILL.md
        ├── SKILL.zh-CN.md
        └── references/
            ├── react-project.md
            ├── standalone-html.md
            └── components/
                ├── general.md
                ├── layout.md
                ├── form-controls.md
                ├── Form.md
                ├── overlays.md
                ├── feedback.md
                ├── Notification.md
                ├── data-display.md
                └── decorative.md
```

## GitHub 发布

进入本目录后初始化 Git 仓库并提交：

```bash
cd animal-island-ui-style
git init
git add .
git commit -m "feat: add animal island ui style skill"
git branch -M main
git remote add origin https://github.com/<your-account>/<your-repo>.git
git push -u origin main
```

发布后可通过 Skills CLI 安装：

```bash
skills add <your-account>/<your-repo>
```

Skill 的标准入口是：

```text
skills/animal-island-ui-style/SKILL.md
```

## 自动同步上游

仓库内置 `.github/workflows/sync-upstream.yml`，会每小时检查
`guokaigdg/animal-island-ui` 的 `main` 分支。上游 Skill 有变化时，Action 会自动更新
`skills/animal-island-ui-style/` 并提交到当前仓库。

也可以在 GitHub 的 **Actions** 页面手动运行
`Sync upstream animal-island-ui skill`，无需等待下一次定时任务。

首次使用前，请在仓库设置中确认：

- **Settings → Actions → General → Workflow permissions** 设置为
  **Read and write permissions**
- 默认分支允许 GitHub Actions 推送提交
- 如果仓库启用了分支保护，需要允许 `github-actions[bot]` 创建同步提交，或改为创建 Pull Request

自动同步只覆盖 `skills/animal-island-ui-style/`，不会覆盖根目录的 `README.md` 和
`LICENSE`。上游提交后最长约一小时同步；GitHub Actions 的定时任务可能因平台调度
存在延迟。

## 使用规范

Skill 会引导 AI 遵守完整的设计约束，核心要点：

- 使用库组件或手写组件时，所有 props 必须来自组件参考，禁止臆造
- 样式统一通过 `--animal-*` CSS tokens 定制，不硬编码颜色
- 控件一律使用胶囊圆角（50px），禁止冷色聚焦环、纯黑文字、原生表单控件
- 图标使用内置 `<Icon name="..." />`，不使用 emoji 或第三方图标库

完整规则见 [SKILL.md](skills/animal-island-ui-style/SKILL.md) 的 Hard rules。

## 许可

本 Skill 遵循上游项目的 CC BY-NC 4.0 许可，仅限非商业使用。详见
[LICENSE](LICENSE)。
