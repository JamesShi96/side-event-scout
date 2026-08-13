# Side Event Scout（通用版 — Claude Code / Codex 均可用）

给定一个大会名称（如 WAIC 2026、ICML 2026、NeurIPS 2026），自动搜集大会期间的**场外非官方周边活动（Side Events）**，按固定表格格式整理、去重、评级，按日期排序输出一份可直接使用的 Side Event List。

**触发方式**：用户说「帮我整理 <大会名> 的 Side Events」（或 side event / 周边活动 / 场外活动）即执行本流程，产出最终表格 + 汇报，不需要中途向用户提问。

## 核心边界（最重要的规则）

**只收大会「场外」的非官方周边活动，绝对不收大会主会场官方日程内的活动。**

- ✅ 要的：阿里云晚宴、MiniMax 线下交流会这类白天逛完正式展会后、偏休闲和 connect 属性的活动——Private Dinner、Mixer、Party、Reception、Happy Hour、Coffee Chat、Café takeover、Coworking、闭门交流、Salon、Demo Night、社群 Meetup、大厂招聘晚宴（Talent Night）
- ❌ 不要的：大会官方 keynote / tutorial / 官方 workshop / 官方展区展台活动；与大会无关的同城活动（活动页没提大会、也看不出面向大会人群）；纯线上活动；纯广告 webinar

判断标准：活动页明确提到大会（@ICML、before WAIC 等），或时间地点+人群明显是冲着大会参会者来的。拿不准的收进去、评级给 C 并在备注说明，不要静默丢弃。

## 工作流

### Step 0 — 理解大会

用网页搜索确认：正式名称与常用缩写（中英文）、举办日期、举办城市与主会场、核心人群（researchers / founders / investors / 开发者）。搜索时间窗 = 大会日期前 3 天到后 1 天（周边活动常在开幕前扎堆）。注意不要混入往届活动，日期必须落在本届时间窗内。

### Step 1 — 发现层：多路搜索（每组都要跑，不要跑一两条就停）

用你可用的网页搜索 + 网页抓取能力，按产出优先级依次执行：

1. **Luma Events Calendar 聚合页**（单页几十个活动，产出最高）：搜 `site:luma.com <缩写> calendar`、直接试 `luma.com/<缩写>`、`luma.com/<缩写><年份>`，以及 "Road to <大会> (Unofficial Community Events)" 这类社区日历。⚠️ Luma 官方日历页是 JS 渲染的，纯 HTTP 抓取只能拿到空壳——如果你有浏览器自动化能力，用它打开真实页面滚动抓全列表；没有的话在报告中如实说明该源未覆盖。社区日历一般可直接抓，但覆盖不全，不能只依赖一个日历
2. **行业媒体盘点文章**（中文活动最高产源）：搜 `<大会名> 场外活动/线下活动/晚宴 盘点/总览/汇总`，重点看雷峰网、机器之心、量子位、新智元——一篇盘点文常覆盖十几个只在微信发布的大厂/基金活动
3. **Luma 站内搜索**：`site:luma.com "<大会名>"`、`site:luma.com <缩写> <年份>`（luma.com 与 lu.ma 都算），多轮换词：afterparty / happy hour / dinner / mixer / reception / social / night / lunch
4. **通用搜索**：`<大会名> side event / mixer / dinner / party / happy hour / reception / meetup`
5. **定向搜高频主办方**：大厂（字节、腾讯、阿里、华为、小米、快手、蚂蚁、MiniMax）和量化基金（九坤、幻方、明汯、衍复、Ubiquant、佳期）几乎每个 AI 顶会都办招聘/学术晚宴，且多数只在微信发布——逐个搜 `<公司名> <大会缩写> 晚宴/交流会/学术之夜`
6. **中文关键词**：`<大会名> 晚宴 / 交流会 / 酒会 / 线下活动`（微信公众号、小红书）
7. **Side Event 聚合站**（中国大会的"Luma 日历"等价物）：WaytoAGI Side Events（如 `waic.waytoagi.com`，按大会找对应子站）、`aieventmap.com`、活动行 `huodongxing.com`。大会结束后聚合站常把历史列表下线——可用 Wayback Machine 快照回溯（`web.archive.org/web/<大会期间时间戳>/<聚合站URL>`），从快照里提取活动详情页链接再逐个抓存档
8. **Luma 城市发现页**：`luma.com/discover` 下对应城市页，扫时间窗内的活动
9. **补充源**：Eventbrite、Partiful、Twitter/X、知乎（开发者社区常发盘点帖）

**停止条件**：连续 2 轮新查询没有发现新活动才算搜完。最后在报告里如实说明搜了哪些源、哪些没覆盖到。

### Step 2 — 整理层：逐个打开活动页录入

对每个候选活动，**必须打开活动页原文**提取信息（搜索结果摘要不足以填表；活动已过期页面通常仍可访问）。信息缺失的字段填 `/`，不要编造。活动页本身缺日期/时间时，可用日历聚合页或盘点文章里标注的日期回填。候选活动多（>20 个）时，如环境支持并行子任务，分批并行抓取。

每个活动按以下 12 列录入：

| # | 列名 | 填写规范 |
|---|------|----------|
| 1 | 日期/Date | `2026.7.27` 格式；多日活动写起始日，跨度写进时间列 |
| 2 | 标题/Name | 活动页原始标题（emoji 可保留） |
| 3 | 主办单位/Org | 逗号分隔多个主办方；个人主办写人名 |
| 4 | 📅时间/Time | `19:00 - 22:00`；跨天写 `7.8 11:30 - 7.9 19:00`；未知写 `TBA` |
| 5 | 📍地点/Location | 场地名 + 地址 + 城市；报名后可见写 `Register to View Address` + 城市 |
| 6 | 简介/Intro | 活动页简介原文或忠实节选（保留人群、议程、嘉宾、规模等关键信息） |
| 7 | 报名/相关链接 | 报名 URL；多个链接并列；无链接写获取方式（如 `email invitation`） |
| 8 | 备注 | 邀请制 / 需审核 / 报名截止 / 仅限特定人群等限制；无则 `/` |
| 9 | 类型/Type | Dinner / Mixer / Party / Reception / Happy Hour / Coffee Chat / Coworking / Salon / Meetup / Talent Night / Workshop / Other，可组合 |
| 10 | Source | Luma / Eventbrite / Partiful / WeChat / 小红书 / Twitter 等，多源并列 |
| 11 | 推荐等级 | S / A / B / C |
| 12 | 推荐原因 | 一句话，点明主办方质量、人群匹配、规模深度中的关键项 |

### Step 3 — 去重

- 主键：标题归一化（去 emoji/标点/大小写）+ 日期；同名同日 = 同一活动
- 同主办方 + 同日 + 同时段 + 同地点，即使标题不同也疑似同一活动多平台发布，合并成一条；保留信息最全的，报名链接和 Source 并列
- 反例：同一主办方的**系列活动**（连办三晚的 dinner、多场次 café 活动）不是重复，按场次分行
- ⚠️ 不要用来源 URL 当去重主键：一篇媒体盘点文章会覆盖多个活动（同 URL ≠ 同活动）；只有活动平台链接（Luma/Partiful/Eventbrite）的同 slug 才能判定同一活动

### Step 4 — 推荐等级 S / A / B / C

综合四个维度：主办方质量（一线 lab/知名基金/明星公司 > 无名）、人群匹配（founders/researchers/investors 精准 > 泛）、规模与深度（小规模/邀请制/curated > 大规模开放）、信息可信度。

- **S**：主办方一线 + 人群高度匹配 + 小规模/邀请制，networking 价值极高
- **A**：主办方质量好、人群匹配，中等规模或开放报名
- **B**：大规模开放，容易参加但人群较泛
- **C**：相关性弱 / 信息不全 / 纯宣传，仍可能有价值，留给读者自判

### Step 5 — 输出

**排序：按日期升序**（推荐等级只是一列信息，不改变排序）。

表格布局（与 ZP 固定模板一致）：
- 第 1 行品牌行：A 列 `感谢大家关注Z Potentials公众号🫱https://postimg.cc/ZBDQTfTS\nFollow us on:\nhttps://www.linkedin.com/company/z-potentials`；E 列 `更多Private Event信息请加入wx社群：\nIf want to marketing your event/expo, plz connect zgroup0515@gmail.com`
- 第 2 行标题行：`<大会缩写>线下活动总览-ZP整理`（附 `信息抓取于 <日期>`）
- 第 3 行表头（上表 12 列）
- 第 4 行起一行一个活动

输出渠道按环境能力选择：
1. **能写飞书**（有 lark-cli 或飞书 MCP）：用 `lark-cli sheets +workbook-create --title "<大会> Side Events" --values -`（stdin 传 JSON 二维数组，含上述品牌行/标题行/表头/数据行）建新表，把表格 URL 给用户
2. **不能写飞书**：输出一个 CSV 文件（同样的行结构）+ markdown 表格，告知用户粘贴进飞书模板即可

最后汇报：表格链接（或文件）、活动总数与分级统计（S×n/A×n/B×n/C×n）、S/A 级活动单独点名、搜索覆盖说明（搜了哪些源、哪些盲区——如微信 only 活动、纯邀请制无公开页面的活动）。

## 已知盲区（报告中如实说明）

- 纯邀请制、无公开页面的活动（如 OpenAI 私密 mixer、HRT email invitation）自动化搜不到，只能靠人脉情报补充
- 微信公众号原文常无法被搜索引擎命中，主要靠行业媒体转载/盘点文覆盖
- 活动信息随时会变（改期、改场地、截止），表格记录的是抓取时点的信息
