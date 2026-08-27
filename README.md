# datahub-cli Skills

`@ucascn/datahub-cli` 随包发布的 Agent Skill 独立发布仓库（不含 CLI 源码，CLI 源码在内网 GitLab）。

## 安装

```bash
npx skills add ucas-cn/datahub-cli-skill -y -g
```

## 结构

```text
skills/datahub-open-api/SKILL.md
```

Skill 由本仓库统一维护（单一事实来源），主仓库 `bom-easy` 以 submodule `cli-skills` 挂载，
`cli/skills` 软链接到本仓库的 `skills/` 目录，修改后直接提交本仓库即可发布。
