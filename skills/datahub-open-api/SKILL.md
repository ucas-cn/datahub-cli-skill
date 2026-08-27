---
name: datahub-open-api
description: 通过 datahub CLI 动态发现并调用 DataHub Open API 与 API Hub 能力。
---

# DataHub API

## 调用流程

1. 使用 `datahub auth status --format json` 检查配置。
2. 使用 `datahub api list open --format json` 或 `datahub api list hub --format json` 发现能力。
3. 使用 `datahub api describe open <path> --format json` 读取接口方法、权限和请求 schema。
4. 根据 schema 构造 JSON 请求体。
5. 使用 `datahub api call open|hub <path> --format json` 调用并解析 stdout。

## 强制规则

- CLI 默认输出 JSON；Agent 可以显式使用 `--format json`，但不要使用其他格式。
- API Key 不得出现在回答、日志或 URL 中。
- Open API 权限使用 `open_api:*`；API Hub 权限使用 `apihub:*`。
- HTTP 403 表示当前 API Key 缺少接口权限，应提示用户联系管理员授权。
- HTTP 401 表示 API Key 缺失或无效，应提示用户检查配置。
- HTTP 400 时重新读取接口 schema，不要猜测字段。
- API Hub 的 `--toexcel` 只在确实需要裸 `dataList` 时使用。
- 同一路径存在多个 HTTP 方法时，使用 `--method GET` 或 `--method POST` 明确方法。
- 不得访问数据库、Cookie 登录或非 `/api/open`、`/api/hub` 的业务路由。

## 示例

```bash
datahub api describe open /material/query --format json
datahub api call open /material/query --data @request.json --format json
datahub api describe hub /ncc/wu_liao_cha_xun --format json
datahub api call hub /ncc/wu_liao_cha_xun --data @request.json --format json
```
