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

Skill 单一事实来源为 `cli` 源码仓库（内网 GitLab）的 `cli/skills/` 物理目录；本仓库（GitHub
`ucas-cn/datahub-cli-skill`）保存 `skills/` 的**物理拷贝**，供 `npx skills add` 独立安装。

## 维护

修改 Skill 后，从 `cli/skills/` 拷贝到本仓库并提交推送：

```bash
cd cli-skills
rm -rf skills && cp -R ../cli/skills skills
git add -A && git commit -m "chore: 同步 datahub-cli skill" && git push
```
