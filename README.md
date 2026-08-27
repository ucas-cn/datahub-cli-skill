# datahub-cli Skills

`@ucascn/datahub-cli` 随包发布的 Agent Skill 独立发布仓库（不含 CLI 源码，CLI 源码在内网 GitLab）。

## 安装

```bash
npx skills add ucas-cn/datahub-cli-skill -y -g
```

## 结构

```text
skills -> ../cli/skills   # 软链接，指向 datahub-cli 源码仓库的 skills 目录
```

Skill 单一事实来源为 `cli` 源码仓库（内网 GitLab）的 `cli/skills/` 物理目录；本仓库（GitHub
`ucas-cn/datahub-cli-skill`）的 `skills` 为软链接，指向该目录。修改 Skill 直接改 `cli/skills/`
后，在本仓库执行 `git add -A && git commit && git push` 即发布。

> 注意：本仓库的 `skills` 是符号链接，独立 clone 本仓库（未同时拉取 `cli` 源码仓库）时
> 软链接目标不可达，`npx skills add` 需在具备 `cli` 源码的完整工作区（如主仓库 `bom-easy`）中验证。
