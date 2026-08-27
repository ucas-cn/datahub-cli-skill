# datahub-cli Skills

`@ucascn/datahub-cli` 的 Agent Skill 独立发布仓库（不含 CLI 源码，CLI 源码在内网 GitLab，CLI 不提供 skill 子命令）。

## 安装

```bash
npx skills add ucas-cn/datahub-cli-skill -y -g
```

## 结构

```text
skills/datahub-cli/SKILL.md
```

`datahub-cli` Skill 覆盖 `datahub` 命令的完整能力面：Open API（`/api/open`）与 API Hub
（`/api/hub`）两套接口的发现、说明与调用，以及认证/配置管理。

本仓库 `skills/` 是 Skill 的**唯一事实来源**，供 `npx skills add` 独立安装。

## 维护

修改 Skill 直接在 `skills/` 编辑，提交并推送本仓库即发布：

```bash
cd cli-skills
git add -A && git commit -m "chore: 更新 datahub-cli skill" && git push
```
