# MCP_INTEGRATION.md

## 使用的 MCP Server

| 项目 | 内容 |
|---|---|
| 提供方 | 麦当劳中国（官方 MCP 服务） |
| 接入地址 | `https://mcp.mcd.cn` |
| 传输协议 | Streamable HTTP（MCP 协议版本 2025-06-18） |
| 鉴权方式 | 请求头 `Authorization: Bearer <MCP_TOKEN>`，Token 在 [open.mcd.cn/mcp](https://open.mcd.cn/mcp) 申请 |
| 限流 | 每 Token 600 次/分钟（429 时降频重试） |

## 实际使用的 Tool 与调用流程

```
用户输入（自拍/情绪/歌曲名）
    │
    ▼
now-time-info                获取当前时间，未给情绪时推断场景
    │
    ▼
query-meals                  查询当前可售菜单，确定今日主餐候选
    │
    ▼
query-meal-detail            查询餐品详情（套餐组成、可换配项）
    │
    ▼
auto-bind-coupons            一键领取麦麦省全部可领券
    │
    ▼
query-my-coupons             确认券到账（指定门店时用 query-store-coupons 校验可用性）
    │
    ▼
calculate-price              传入商品列表+最优券，计算应付价与节省金额
    │
    ▼
生成 H5 点唱页（注入真实菜单/券/价数据，产出分享卡片）
    │
    ▼
create-order（可选）         用户明确说「就点这个」才调用；调用前复述门店、餐品、金额并等确认
```

## 业务价值

| 工具 | 在 McTrack 里的价值 |
|---|---|
| query-meals / query-meal-detail | 「今日主餐」来自真实在售菜单而非臆造，情绪匹配落到可点的餐品上 |
| auto-bind-coupons / query-my-coupons | 把「领券」从用户自己翻 app 变成点唱附带动作，卡片直接显示省了多少 |
| calculate-price | 最省点法可量化：本顿应付价、立省金额直接上分享卡片 |
| create-order | 从「好玩」到「真香」的闭环：看完卡片一句话下单 |

## 错误处理

- **401**：Token 失效/未配置 → 提示用户重新激活 Token，H5 降级为示例数据
- **429**：触发限流 → 降低调用频率重试
- **超时/不可用**：不中断体验，H5 使用内置示例数据集并标注「演示数据」

## 配置示例

见 `mcp-config.example.json`（仅环境变量占位符，无真实 Token）。
