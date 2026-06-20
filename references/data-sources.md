# 数据源清单

## 三通道检索体系

| 通道 | 工具 | 覆盖范围 | 最佳用途 | 费用 | 优先级 |
|---|---|---|---|---|---|
| WebSearch | Claude Code 内置 `WebSearch` | 中文内容、国内政策、行业报告、公司新闻、海外市场 | 国内外通用搜索主力 | 免费（内置） | 必选 |
| Wind | `wind-mcp-skill`（走 Skill 工具） | 宏观指标、行业统计、上市公司财务、公告、财经新闻、指数/板块数据 | 结构化金融数据 | 每日免费1000积分 | 推荐 |
| Semantic Scholar | Bash `curl` + S2 API | 英文学术论文、综述/高引/最新论文、引用趋势 | 硬科技学术引擎 | 免费（注册即用） | 推荐（仅消费/周期/简单制造跳过） |

## 通道路由规则

- 国内政策/行业数据/中文报告/公司新闻 → **WebSearch 主力**
- 海外市场/全球对标/英文报告 → **WebSearch 主力 + S2 辅助**
- 宏观指标/行业统计数据/上市公司财务/公告/指数行情 → **Wind 主力**（无 Wind 时 WebSearch + 公开财报兜底）
- 技术原理/学术前沿/论文验证 → **Semantic Scholar 主力 + WebSearch 交叉验证**
- 论文结论用 WebSearch 搜索产业验证
- **禁止**把 WebFetch 当搜索引擎用（仅用于读取已知 URL）

## Wind（万得）使用要点

> **费用说明**：Wind MCP 每日免费赠送 1000 积分，单次查询消耗 1-5 积分，日常行研完全够用。注册安装见 [wind-mcp-skill](https://github.com/anthropics/skills/tree/main/skills/wind-mcp-skill)。

- 调用方式：通过 Skill 工具调用 `wind-mcp-skill`，传入自然语言查询
- 核心工具（按行研板块对应）：

| 板块 | Wind server_type + tool | 典型取数场景 |
|---|---|---|
| §2.2 产业链价值链 | `stock_data.get_stock_fundamentals` / `analytics_data.get_financial_data` | 上市公司毛利率/净利率、分业务收入 |
| §2.3 竞争格局 | `stock_data.search_stocks` + `get_stock_fundamentals` | 行业内上市公司筛选、营收/市值/ROE 对标 |
| §2.4 市场规模 | `economic_data.get_economic_data` | 行业产量/销量/产值、宏观驱动因子（GDP/PMI/社融） |
| §2.5 PEST | `economic_data.get_economic_data` + `financial_docs.get_financial_news` | 宏观经济指标、政策相关新闻 |
| §2.6 景气度 | `economic_data.get_economic_data` | 高频行业指标（产量/价格/开工率/库存） |

- `economic_data` 参数：`metricIdsStr` 用自然语言描述指标（如"中国新能源汽车产量"），`freq` 选月/季/年，日期格式 `yyyyMMdd`
- `financial_docs` 参数：`query` 用自然语言（不含空格），`top_k` 控制返回条数
- 股票筛选/专项 NL 工具参数：`question` 用自然语言
- **注意**：Wind 不覆盖欧股/日股/汇率/期货盘口/加密货币，这些场景用 WebSearch 补

## Semantic Scholar API

| 配置 | 值 |
|---|---|
| API Key | `$S2_API_KEY`（Header: `x-api-key`） |
| 注册地址 | https://www.semanticscholar.org/product/api （免费，1 分钟注册） |
| Base URL | `https://api.semanticscholar.org/graph/v1` |
| 搜索端点 | `/paper/search/bulk`（批量搜索，布尔语法、年份过滤、引用排序、分页 token） |
| 详情端点 | `/paper/{id}`（tldr/embedding/citations/references） |
| Rate Limit | **1 RPS**（每次请求后 `sleep 1.1s`，429 时 sleep 5s 重试最多 3 次） |
| 搜索字段 | title,abstract,year,citationCount,authors,url,externalIds,publicationVenue,openAccessPdf,publicationTypes,fieldsOfStudy |

检索策略：
- 多 query：英文术语 + 缩写 + `survey/review` + 中文术语（WebSearch 互补）
- 过滤：`year=2021-2026`、`minCitationCount=3`、`sort=citationCount:desc`
- 多个 query 结果按 `paperId` 去重后按 `citationCount` 降序
- 摘要+tldr 不足时才 WebFetch PDF

## 推荐数据源（按优先级）

### 官方
- 国家统计局 stats.gov.cn、工信部、商务部、央行、各部委
- 行业协会（中汽协、信通院、半导体行业协会、白酒协会等）
- 上市公司年报/招股说明书（巨潮资讯网 cninfo.com.cn、SEC EDGAR）

### 研究机构/数据库
- 券商研报（东方财富、慧博、发现报告、Choice、Wind 资讯）
- Frost & Sullivan、IDC、Gartner、艾瑞、灼识、易观
- CEIC、Wind、同花顺、Choice、Bloomberg

### 行业自有数据源（按需）
- 白酒：今日酒价
- 影视：猫眼专业版、Box Office Mojo
- 医药：药智网、米内网
- 房产：克而瑞
- 大宗：我的钢铁、上海有色
- 债券：DM 平台
