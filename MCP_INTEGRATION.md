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
用户说一件今天发生的事（"甲方让我改了第八版方案"）
    │
    ▼
WorkBuddy 理解原话          归类事件 → 犒劳指数（定份量）+ 口味偏好 + 写一句判词
    │
    ▼
order-list                   查近期订单，同档位里优先批准你真正常点的（"你点过 12 次"）
    │
    ▼
query-nearby-stores          定位门店
    │
    ▼
query-meals                  在售菜单里按关键词匹配批准餐品（名称/图片/现价）
    │
    ▼
list-nutrition-foods         查热量，批准书上如实标注
    │
    ▼
auto-bind-coupons            一键领取麦麦省全部可领券
    │
    ▼
query-my-coupons             确认券到账（指定门店时用 query-store-coupons 校验）
    │
    ▼
calculate-price              传入商品列表 + 最优券，得券后价与节省金额
    │
    ▼
生成「麦麦犒劳批准书」H5     注入事由/判词/餐品/热量/券后价，打开即盖章
    │
    ├─ query-lottery-info → draw-lottery（可选）   有抽奖次数且用户同意时"加赏"一次
    │
    ▼
create-order                 用户说「就吃这个」才调用；先复述门店、餐品、金额并等确认
```

## 业务价值

| 工具 | 在麦麦批准里的价值 |
|---|---|
| order-list | 批准你真正爱吃的，而不是系统猜的：同档位里优先常点餐品，批准书写上"你点过 N 次" |
| query-meals | 批准的是真实在售、能点到的餐品，不是臆造 |
| list-nutrition-foods | 热量写在批准书上，份量跟犒劳指数挂钩，犒劳但不过量 |
| auto-bind-coupons / query-my-coupons | 去掉"花钱心疼"这层负罪感：券自动领好，不用自己翻 app |
| calculate-price | 券后价上批准书，"批准金额 ¥27.5"一眼可见 |
| query-lottery-info / draw-lottery | 真实积分抽奖做"加赏"，惊喜是真的 |
| create-order | 从"被批准"到"吃到嘴"一句话闭环，返回支付链接 |

## 错误处理

- **401**：Token 失效/未配置 → 提示用户重新激活 Token，H5 降级为菜单快照
- **429**：触发限流 → 降低调用频率重试
- **超时/不可用**：不中断体验，H5 使用内置快照并标「门店价」
- **菜单图加载卡住**：2.5 秒未出图自动换成插画，不留空白

## 配置示例

见 `mcp-config.example.json`（仅占位符，无真实 Token）。
