---
name: datahub-cli
description: >
  通过 datahub CLI（@ucascn/datahub-cli，命令 datahub）动态发现并调用 DataHub 开放接口
  （/api/open/*）与 API Hub 查询脚本（/api/hub/execute/*）。当需要查询/调用 DataHub 的
  开放接口或 API Hub 能力、检查 API Key 认证配置、按接口 schema 构造请求体时触发。
  接口清单由远端 OpenAPI 文档动态发现，本 skill 不维护固定接口清单。
---

# DataHub CLI（datahub）

通过 `datahub` 命令调用 DataHub 开放接口与 API Hub。接口清单由远端 OpenAPI 文档
（/api/open/docs.json 与 /api/hub/docs.json）动态发现，CLI 不维护业务接口固定代码。

## 调用流程

1. 检查配置：`datahub auth status --format json`（确认 `reachable` 与 `apiKeyValid`）。
2. 发现能力：`datahub api list open --format json` 或 `datahub api list hub --format json`。
3. 读取接口详情：`datahub api describe open|hub <path> --format json`（含 method、permission、requestSchema）。
4. 按 `requestSchema` 构造请求体并调用：`datahub api call open|hub <path> --data @request.json --format json`。
5. 解析 stdout JSON 返回结果。

## 命令参考

| 命令 | 作用 |
|------|------|
| `datahub auth init` | 初始化 Base URL 与 API Key（交互式询问；非交互用 --base-url/--api-key 或环境变量） |
| `datahub auth status` | 检查服务连通与 API Key 有效性 |
| `datahub config show` | 查看当前配置（API Key 脱敏） |
| `datahub config set base-url <url>` | 设置服务地址 |
| `datahub config set api-key <key>` | 设置 API Key |
| `datahub config path` | 查看配置文件路径 |
| `datahub api list open\|hub` | 查看接口列表 |
| `datahub api describe open\|hub <path>` | 查看接口详情 |
| `datahub api call open <path>` | 调用 Open API |
| `datahub api call hub <group>/<modName>` | 调用 API Hub |
| `datahub api refresh open\|hub` | 强制刷新接口文档缓存 |
| `datahub version` | 查看 CLI 版本 |

通用选项：`--format json`（CLI 默认即 JSON、唯一支持格式）、`--base-url`/`--api-key`（单次覆盖配置）、
`-h`/`--help`、`-v`/`--version`。

## 认证与配置

配置优先级：命令行参数 > 环境变量 > 配置文件 > 默认值。

- 环境变量：`DATAHUB_BASE_URL`、`DATAHUB_API_KEY`
- 默认 Base URL：`https://work.ucas.com.cn:4011`
- 配置文件：`~/.config/datahub/config.json`（macOS/Linux）、`%APPDATA%/datahub/config.json`（Windows），权限 0600
- 文档缓存：`~/.cache/datahub/open/<hash>.json` 与 `~/.cache/datahub/hub/<hash>.json`；`api list/describe` 可优先用缓存，`api refresh` 强制拉远端

认证输入只有两项：`api-key`（经 `X-API-Key` 请求头发送）与 `base-url`。

## 请求输入

- POST：`--data '<json>'`（内联）、`--data @file.json`（读文件）、`--data -`（读 stdin）
- GET：`--param key=value`（可重复，同名 key 聚合为数组）
- 同一路径多个 HTTP 方法时用 `--method GET|POST` 明确，或使用 `POST /path` 形式
- API Hub 可加 `--toexcel`：默认返回 `{ success, data, ... }` 信封（`data` 内含模块结果），`--toexcel` 时裸返回 dataList 数组，便于直接取数

## 输出与退出码

- 成功：服务端 JSON 写 stdout，退出码 0
- 失败：`{ "ok": false, "error": { "message", "exitCode", ... } }` 写 stderr，退出码非 0

| 退出码 | 含义 |
|---|---|
| 0 | 成功 |
| 1 | 未分类错误 |
| 2 | 参数错误 |
| 3 | 配置缺失或无效 |
| 4 | 网络错误 |
| 5 | API Key 缺失或无效（401） |
| 6 | 权限不足（403） |
| 7 | 远端 API 其他错误 |
| 8 | 响应不是合法 JSON |

## 错误处理

- 退出码 3：未配置 API Key，执行 `datahub auth init` 或 `datahub config set api-key`
- 退出码 5（401）：API Key 缺失/无效，提示用户检查配置
- 退出码 6（403）：当前 API Key 缺少接口权限，提示联系管理员授权（Open API 为 `open_api:*`，API Hub 为 `apihub:*`）
- 退出码 2：CLI 参数错误，修正命令参数（`--data` JSON、`--param key=value`、接口标识）
- HTTP 400（退出码 7）：重新 `api describe` 读取 schema，不猜测字段
- 退出码 7（其他远端错误）：服务端异常（400/404/5xx 等），保留错误信息上报

## 强制规则

- CLI 默认输出 JSON，Agent 显式使用 `--format json`，禁止使用其他格式
- API Key 不得出现在最终回答、日志或 URL 中；展示一律脱敏
- 接口权限码：Open API 为 `open_api:*`、API Hub 为 `apihub:*`，不得与业务权限码混用
- 不得访问数据库、Cookie 登录，或 `/api/open`、`/api/hub` 之外的业务路由
- 接口清单以远端 docs.json 为准，不维护本地接口固定代码

## 示例

```bash
# 检查配置
datahub auth status --format json

# 发现接口
datahub api list open --format json
datahub api list hub --format json

# 查看接口详情（方法/权限/请求 schema）
datahub api describe open /material/query --format json
datahub api describe hub /ncc/wu_liao_cha_xun --format json

# POST 调用（数据来自文件）
datahub api call open /material/query --data @request.json --format json

# POST 调用（数据内联）
datahub api call hub /ncc/wu_liao_cha_xun --data '{"keyword": "xxx"}' --format json

# GET 调用（参数化，路径为示意，以 api list open 实际接口为准）
datahub api call open /some/get --param key=value --method GET --format json

# API Hub 裸 dataList
datahub api call hub /ncc/wu_liao_cha_xun --data @request.json --toexcel --format json
```
