# Side Event List 固定表格格式

以 ZP 整理的《ICML 2026 Reception》表为准（https://my.feishu.cn/wiki/OWWdwceRjihZXtk6HemctHPrnuc），前 8 列与其完全一致，后 4 列为本 skill 追加。

## 表格布局（飞书表格）

- 第 1 行：品牌行 —— A 列 `感谢大家关注Z Potentials公众号🫱https://postimg.cc/ZBDQTfTS\nFollow us on:\nhttps://www.linkedin.com/company/z-potentials`；E 列 `更多Private Event信息请加入wx社群：\nIf want to marketing your event/expo, plz connect zgroup0515@gmail.com`
- 第 2 行：标题行 —— `<大会缩写>线下活动总览-ZP整理`（如 `WAIC 2026线下活动总览-ZP整理`），可附一句 `信息抓取于 <日期>`
- 第 3 行：表头（见下）
- 第 4 行起：一行一个活动

## 列定义

| # | 列名 | 填写规范 |
|---|------|----------|
| 1 | 日期/Date | `2026.7.27` 格式。多日活动写起始日，跨度写进时间列 |
| 2 | 标题/Name | 活动页原始标题，保留原文（含 emoji 可保留） |
| 3 | 主办单位/Org | 逗号分隔多个主办方；个人主办写人名 |
| 4 | 📅时间/Time | `19:00 - 22:00`；跨天写 `7.8 11:30 - 7.9 19:00`；未知写 `TBA` |
| 5 | 📍地点/Location | 场地名 + 地址 + 城市，换行分隔；报名后可见写 `Register to View Address\n<城市>` |
| 6 | 简介/Intro | 活动页简介原文或忠实节选（保留关键信息：人群、议程、嘉宾、规模） |
| 7 | 报名/相关链接 | 报名 URL；多个链接换行并列；无链接写获取方式（如 `email invitation`） |
| 8 | 备注 | 邀请制 / 需审核 / 报名截止时间 / 仅限女性等限制条件；无则 `/` |
| 9 | 类型/Type | Dinner / Mixer / Party / Reception / Happy Hour / Coffee Chat / Coworking / Salon / Meetup / Talent Night / Workshop / Other，可组合（如 `Mixer / Party`） |
| 10 | Source | Luma / Eventbrite / Partiful / WeChat / 小红书 / Twitter 等，多源并列 |
| 11 | 推荐等级 | S / A / B / C（评级标准见 SKILL.md Step 4） |
| 12 | 推荐原因 | 一句话，点明主办方质量、人群匹配、规模深度中的关键项 |

## 排序

**按日期升序**（与 ZP 原表一致），同日内顺序不限。推荐等级是列信息，不参与排序；S/A 级活动在最终汇报里单独点名即可。

## 示例行

取自 ICML 2026 真实数据（前 8 列）+ 追加列示例：

| 日期/Date | 标题/Name | 主办单位/Org | 📅时间/Time | 📍地点/Location | 简介/Intro | 报名/相关链接 | 备注 | 类型/Type | Source | 推荐等级 | 推荐原因 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 2026.7.6 | ICML Dinner with VESSL AI | VESSL AI, Terry Kim (incoming MIT PhD), Sehoon Kim (incoming KAIST Assistant Professor) | 19:00 - 22:00 | Register to View Address\nSeoul, South Korea | Join us for a curated dinner bringing together a select group of PIs and PhDs during ICML 2026 in Seoul. This is a private gathering of 15-20 researchers… | https://luma.com/vessl-9xxv | 18 人上限，curated 名单 | Dinner | Luma | S | 主办方与嘉宾质量高，15-20 人 curated 私密晚宴，人群精准，深度 networking 价值极高 |
| 2026.7.5 | SMW AI Networking Mixer before ICML | SEOUL META WEEK 2026 | 16:00 - 20:00 | Market 'O' Apgujeong | AI Builders Mixer before ICML Seoul brings together global AI researchers, builders, founders, and innovators… | https://www.eventbrite.com/e/smw-ai-networking-mixer-before-icml-tickets-1990157284010 | 需预先报名 | Mixer | Eventbrite | B | 开放报名易参加，但规模大、人群较泛，深度 networking 价值一般 |

## 写入飞书的实现要点

- 用 `lark-sheets` 的 `+workbook-create --values -`（stdin 传 JSON 二维数组）一步建表 + 写入；列全部为文本（日期列保持 `2026.7.27` 文本格式，与 ZP 原表一致），所以用 untyped 的 `--values` 即可，不需要 `--sheets` typed 协议；后续修正数据用 `+csv-put --start-cell A1` 整体覆写
- sheet 名：`<大会缩写> <年份> Side Events`（如 `WAIC 2026 Side Events`）
- 不做合并单元格（ZP 原表按日期合并是人工美化，非必需）；如需美化，表头行加背景色即可
- 建完表把 URL 交给用户；如用户指定了目标文件夹或 wiki 位置，用 `--folder-token` 或后续用 lark-wiki / lark-drive 移动
