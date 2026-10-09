# workbuddy.md — WorkBuddy 开发对话上下文导出

> 本文件为使用腾讯 WorkBuddy 开发「麦乐点唱机 McTrack」过程中的对话上下文节选，用于 WorkBuddy 联动活动核验。

**时间**：2026-10-09 14:45 — 15:50（Asia/Shanghai）
**环境**：macOS · WorkBuddy Agent 模式 · 麦当劳 MCP（mcd-mcp）

---

**用户**：我想参加这个活动：https://mp.weixin.qq.com/s/-6lOK0oh3hoM-mj7YbiAog

**WorkBuddy**：（拉取公众号文章与 GitHub 官方仓库规则）这是 2026 麦当劳程序员创意开发大赛：基于麦当劳 MCP 开发 Skill，GitHub 公开仓库提交，按 Star 排名，10/25 截止。参赛需备齐 README.md、CONTEST_DECLARATION.md、MCP_INTEGRATION.md、mcp-config.example.json、源代码，参加 WorkBuddy 专项奖励需提交本文件。随后给出参赛路径：用户申请 MCP Token → WorkBuddy 配连接器 → 开发 Skill → 建仓提交 → 提 Issue 报名。

**用户**：（选择选题方向）我想做与 bgm 有关的 product

**WorkBuddy**：提出三个概念（麦麦电台 / 今日麦麦主题曲 / 等餐BGM），推荐「音乐 × 餐品」方向：说心情/在听的歌 → 判定音乐人格 → MCP 匹配今日主餐 → Web Audio 生成专属 BGM → 领券算价 → 分享卡片 → 可选下单。

**用户**：麦丹劳的MCP有什么能力？

**WorkBuddy**：（已提前拉取 M-China/mcd-mcp-server 仓库）列出 33 个工具六块能力：点餐全链路（菜单/算价/下单/取消/查单/门店/营养）、优惠券（一键领券/我的券）、积分（账户/抽奖）、麦麦商城、活动日历与主题活动预约、时间工具。并标注 McTrack 主干将用到 auto-bind-coupons → query-meals → calculate-price → create-order。

**用户**：怎么申请 token，另外，我想做一个有"化学反应"的小项目。这个项目在哪一个端，什么场景？做到什么效果？首先得是好玩的，脑洞大开，但本质是实用的，符合吃或玩的目的。

**WorkBuddy**：给出 Token 申请五步（open.mcd.cn/mcp 登录→控制台→激活→复制）。概念初稿「人体化学反应实验室」并以 H5 草图展示效果。

**用户**：我不是真的指化学反应

**WorkBuddy**：确认用户指的是「两样东西碰撞产生第三种产物」，提供选项：音乐×餐品 / 食物×食物 / 人×人 / 其他。

**用户**：（选择）音乐 × 餐品

**WorkBuddy**：定稿「麦乐点唱机 McTrack」完整方案：端=WorkBuddy Skill + 手机端 H5；场景=点单前选择困难与社群分享；效果=情绪→主题曲→主餐→领券算价→分享卡片→一键下单。

**用户**：（发送 MCP Token，已脱敏）MCD_MCP_TOKEN_REDACTED

**WorkBuddy**：收到 Token。读取本机 `~/.workbuddy/mcp.json`，保留已有 ardot 连接器，合并写入 mcd-mcp（streamablehttp / https://mcp.mcd.cn / Bearer 鉴权）。用 curl 向 mcp.mcd.cn 发起 MCP initialize 握手验证通过（serverInfo: mcd-mcp v1.0.0，协议 2025-06-18），随后 tools/list 确认真实工具可用（query-my-coupons、商城与券工具返回正常）。

**用户**：今日麦麦主题曲：自拍->情绪关键词->匹配对应麦丹劳某一首主题曲->匹配「今日主餐」->领取优惠卷->生成分享卡片->一键下单

**WorkBuddy**：按该流程重写 H5 为两步：①拍自拍或点情绪关键词（WorkBuddy 对话里发自拍由 AI 视觉读情绪）②选歌（选填）→ 生成卡片。卡片含实验编号、主题曲名、情绪×麦当劳=今日最搭反应式、主餐+匹配度、MCP 最省点法、Web Audio 播放器、下单按钮。内置 8 种音乐人格（急行电子/深夜 Lo-Fi/阳光摇摆/律动电子/治愈民谣/车库摇滚/慢拍爵士/独立流行），BPM 68-140、不同调式与摇摆度，黑胶噪声+低通滤波做 lo-fi 质感；匹配度用日期哈希保证同一天可复现。node --check 校验内嵌 JS 语法通过、注入占位符 `__MCTRACK_DATA__` 唯一。

**WorkBuddy**：拉取官方原版 CONTEST_DECLARATION.md（未改动），编写 README.md、MCP_INTEGRATION.md、mcp-config.example.json（脱敏占位符），最后 git 建仓推送并提交参赛 Issue。

---

**WorkBuddy 侧技术动作清单**：WebFetch 官方规则 ×4 / 写入本机 MCP 配置并 curl 握手验证 / H5 单文件开发（含 Web Audio 合成器）/ 官方声明文件拉取 / 文档三件套 / 仓库初始化推送 / Issue 报名。
