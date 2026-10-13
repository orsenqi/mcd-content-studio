# WorkBuddy 对话上下文

本文件记录「麦麦内容工坊 mcd-content-studio」在腾讯 WorkBuddy 智能体中的真实开发过程，用于 WorkBuddy 专项奖励核验与活动联动。

## 一、基本信息

| 项目 | 内容 |
|---|---|
| 开发工具 | 腾讯 WorkBuddy 智能体 |
| 使用形态 | 连接器（自定义 MCP）+ 会话式开发 |
| 项目仓库 | mcd-content-studio |
| 开发时间 | 2026 年 10 月 |

## 二、WorkBuddy 在本项目中的实际用途

本项目的**全部开发过程**在 WorkBuddy 会话中完成，具体承担三类工作：

1. **MCP 接入与实测** —— 在 WorkBuddy 中配置 `mcd-mcp` 自定义连接器，完成 `initialize` 握手、`tools/list` 枚举与多轮 `tools/call` 实测；
2. **Skill 开发** —— 依据实测结论编写 `SKILL.md` 工作流、`README.md`、`MCP_INTEGRATION.md` 等文件；
3. **数据链路验证** —— 真实调用麦当劳 MCP 拉取门店、菜单、活动、营养数据，每轮结果均回写文档。

## 三、关键对话上下文（开发过程实录）

### 1. 需求确立

> **用户**：用麦当劳 MCP 做一个内容创作方向的 Skill，面向美食博主和门店运营，不要太重。
>
> **WorkBuddy**：确认方向为「一稿多平台」——同一份 MCP 真实数据，分别产出小红书、公众号、口播三套体例。

### 2. MCP 连接配置

WorkBuddy 输出可直接粘贴的连接器配置，并在写入本地前完成脱敏处理：

```json
{
  "mcpServers": {
    "mcd-mcp": {
      "type": "streamablehttp",
      "url": "https://mcp.mcd.cn",
      "headers": { "Authorization": "Bearer <MCP_TOKEN>" }
    }
  }
}
```

### 3. 工具枚举与实测

> **WorkBuddy**：服务端返回 35 个工具，其中 `query-promotions` 与 `query-survey-coupon` 未见于官方公开文档。
>
> **WorkBuddy**：实测 `campaign-calendar`、`list-nutrition-foods`、`now-time-info` 三个免参数工具，均返回真实数据。
>
> **WorkBuddy**：实测发现 `query-meals` 强制要求 `storeCode`，必须先解析门店——原工作流缺此步骤，需补全。

### 4. 数据链路跑通

| 调用 | 实测入参 | 结果 |
|---|---|---|
| `query-nearby-stores` | `beType=1, searchType=2, city=广州, keyword=体育西路` | 取得 `storeCode=1400011` |
| `query-meals` | `storeCode=1400011, orderType=1, beType=1` | 返回在售菜单，含现价与划线价 |
| `campaign-calendar` | 无参数 | 返回进行中的真实活动 |
| `list-nutrition-foods` | 无参数 | 返回 160 项营养数据 |

### 5. 文档定稿

WorkBuddy 依据实测结论修正 `SKILL.md`（补全门店解析步骤），并补齐官方要求提交的全部文件。

## 四、WorkBuddy 能力使用清单

| WorkBuddy 能力 | 本项目中的使用 |
|---|---|
| 自定义 MCP 连接器 | 接入麦当劳官方 MCP Server，完成鉴权与工具调用 |
| 会话式开发 | 工作流设计、文件撰写、结论复核 |
| 本地文件读写 | 生成并维护仓库全部文件 |
| 版本管理 | 通过 Git 记录多次提交，保留改动历史 |
| 网络检索 | 核对大赛官方规则原文与实时榜单 |

## 五、声明

本文件内容为该项目在 WorkBuddy 中的真实开发记录，用于活动核验。所有 Token、手机号等敏感信息均已脱敏，仓库中不含任何真实凭证。
