# FreeChemDraw MCP Server

[English](README.md) | [简体中文](README.zh-CN.md) | [日本語](README.ja.md)

**面向 AI 智能体的化学结构工具**

FreeChemDraw MCP Server 官方文档及接入示例，介绍远程化学结构转换、分子性质计算和二维结构绘图。

本仓库提供 FreeChemDraw 托管 MCP 服务的文档和接入示例。服务端实现由官方私有维护。

[FreeChemDraw](https://freechemdraw.com/) |
[产品介绍](https://freechemdraw.com/en/mcp/) |
[官方托管平台](https://ai.freechemdraw.com/) |
[官方文档](https://ai.freechemdraw.com/docs/)

Remote MCP 地址：`https://ai.freechemdraw.com/mcp`

## 快速开始

1. 选择支持远程 Streamable HTTP 的客户端。
2. 将服务地址设置为：

   ```text
   https://ai.freechemdraw.com/mcp
   ```

3. 连接、发现工具，然后通过客户端调用以下乙醇示例：

   ```json
   {"name":"calculate_properties","arguments":{"structure":"CCO","input_format":"smiles"}}
   ```

该对象是 `tools/call` 的参数，不是完整的 HTTP/JSON-RPC 请求。初始化和协议协商由客户端处理。
返回性质属于计算结果，不等同于实验测量。参见[入门指南](docs/getting-started.md)。

## 什么是 FreeChemDraw MCP？

FreeChemDraw MCP Server 是面向 AI 智能体和应用的托管服务，提供方为 FreeChemDraw，
类别为远程 MCP 服务 / 化学 REST API。它使用模型上下文协议（Model Context Protocol，MCP）
及 Streamable HTTP，让兼容客户端发现并调用化学工具。本仓库仅包含使用指南和请求示例，不提供服务端实现或化学计算引擎。

## 功能

- 当前公共契约提供免费、匿名且受限流保护的基础化学调用，无需注册或 API Key。
- 规范化 SMILES，转换 SMILES/MOL 结构。
- 计算分子式、分子量、精确质量、形式电荷、LogP 和 TPSA。
- 生成 SVG/PNG 二维分子结构图。
- 通过 Remote MCP 或 REST 将化学处理接入 AI 智能体工作流。

访问策略和实际限额以托管部署为准，请检查[能力发现](https://ai.freechemdraw.com/api/v1/capabilities)及当前[服务文档](https://ai.freechemdraw.com/docs/)。

## 可用工具

| 核心工具 | 功能 |
| --- | --- |
| `resolve_chemical` | SMILES 规范化、分子式及可选性质 |
| `convert_structure` | SMILES/MOL 结构转换 |
| `calculate_properties` | 分子式、分子量、精确质量、形式电荷、LogP 和 TPSA |
| `render_structure` | 使用默认风格生成 SVG/PNG 二维结构图 |

`get_editor_link` 是可选工具，仅在部署启用时出现。它要求用户明确同意
（`allow_temporary_storage=true`）加密临时存储结构，用于一次性导入；链接 10 分钟后失效。
使用前检查 `tools/list`。也可以手动将 SMILES/MOL 导入在线编辑器，
参见[工具](docs/tools.md)及[隐私](docs/privacy.md)。

## 在 FreeChemDraw 中继续编辑

1. 请智能体返回规范化的 SMILES，或通过 `convert_structure` 设置 `output_format=mol` 获取 MOL 结构。
2. 打开 [FreeChemDraw 在线编辑器](https://freechemdraw.com/)，粘贴 SMILES 或导入保存的 MOL 文件。
3. 在编辑器中检查并继续修改结构。MOL 转为 SMILES 会丢失坐标和注释；需要保留这些信息时使用 MOL。

只有实际部署的 `tools/list` 中出现 `get_editor_link` 时，才能使用自动编辑链接。
该工具要求用户明确同意加密临时存储结构，链接仅可使用一次，10 分钟后失效。
工具未开放或用户不同意临存时，使用手动导入。本仓库不承诺客户端与编辑器之间自动同步。
参见[工具](docs/tools.md)和[隐私](docs/privacy.md)。

## MCP 客户端配置

[配置示例](examples/mcp-client-config.json)是通用连接描述：`transport=streamable-http`，地址如上。
请在客户端当前版本的远程服务设置中填写这些值；JSON 字段因客户端而异。
它不是 Claude、Cursor 或其他产品已认证、可直接导入的配置。
不要使用 stdio 命令或旧的仅 SSE 传输，参见[远程 MCP](docs/remote-mcp.md)。

## REST API

向托管化学 REST API 发送 JSON；REST 不使用 MCP 请求封装：

```bash
curl --fail-with-body https://ai.freechemdraw.com/api/v1/properties/calculate -H 'Content-Type: application/json' --data '{"structure":"CCO","input_format":"smiles"}'
```

成功响应包含 `request_id`、`data`、`error=null` 和 `usage.calls=1`。
`usage.calls` 是调用计数，不是收费额度。参见 [REST API](docs/rest-api.md)及托管[OpenAPI 契约](https://ai.freechemdraw.com/openapi/v1.json)。

## 使用限制

免费基础服务受限流保护，不承诺服务等级。初始化和工具发现也计入使用量；
收到 HTTP 429 时遵循 `Retry-After` 并退避重试。
详细参考限额和错误处理见[使用限制](docs/usage-limits.md)，生产环境实际配置以当前[官方服务文档](https://ai.freechemdraw.com/docs/)为准。

## 隐私

请求会将结构发送到托管服务处理。普通计算不持久化原始结构或图片，但按服务策略保留调用元数据
（参考保留期为 30 天）。客户端与代理可能单独保留数据。可选编辑器链接仅在用户明确同意后加密临存结构。
远程处理不构成绝对的保密保证，参见[隐私说明](docs/privacy.md)和[官方隐私政策](https://ai.freechemdraw.com/privacy)。

## 文档导航

- [入门指南](docs/getting-started.md)
- [工具、参数与返回字段](docs/tools.md)
- [远程 MCP 与客户端配置](docs/remote-mcp.md)
- [REST API](docs/rest-api.md)
- [使用限制与错误](docs/usage-limits.md)
- [隐私](docs/privacy.md)
- [故障排查](docs/troubleshooting.md)
- [常见问题](docs/faq.md)
- [变更记录](CHANGELOG.md)
- [贡献指南](CONTRIBUTING.md)
- [安全报告](SECURITY.md)

## 已知限制

名称/CAS 查询不可用。仅默认绘图风格经过验证，不支持 ACS 专用样式。
可选编辑器集成不是始终开放的第五个核心工具。本项目不声称获得客户端产品认证、提供无限使用，
或对所有分子完成科学验证。本仓库不分发服务端实现，也不授予其使用许可证。

## 问题反馈与贡献

通过 [Issues](https://github.com/chemaiatlas/freechemdraw-mcp/issues)提交文档修正、使用公开结构的最小问题复现和功能建议。
贡献范围为文档与可安全分享的接入示例，参见[贡献指南](CONTRIBUTING.md)。
漏洞请按 [SECURITY.md](SECURITY.md)私密报告。产品品牌为 FreeChemDraw，仓库托管在 ChemAI Atlas Organization。
