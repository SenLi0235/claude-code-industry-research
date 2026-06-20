# 行业研究 Skill — Claude Code 行研自动化

> 让 AI 按投行标准写行业研究报告：系统的分析框架 + 可溯源的数据 + 透明的计算过程 + 诚实的不确定性披露。

## 技能亮点

### 三段审批窗口 —— 不再"一键盲出"

传统 AI 行研是一次性输出，错了只能重来。本 skill 把行研拆成 **3 次审批窗口**：
- **Window A**：先确认行业定义边界和生命周期阶段（基础都错了后面全废）
- **Window B**：竞争格局和市场规模**先交提纲**，你批准了再写正文（两个最容易翻车的板块）
- **Window C**：PEST + 景气度并发写，最后统一审全文

对比旧版 7 次确认 → 3 次窗口，审批疲劳感大幅降低，但关键决策一个不落。

### `[UNSOURCED]` 标记 —— 找不到源就老实说找不到

AI 行研最大的坑是**编数字**。本 skill 强制要求：找不到可靠来源的数据，正文中标注 `[UNSOURCED]`，绝不捏造。板块内 `[UNSOURCED]` 超过 30% → 系统自动退回重写（说明检索不够深）。最后附录 A 统一列出所有不确定性，你一次性处理。

### 预分配编号段 —— 4 个板块可以并发写

传统 AI 行研是串行的（写完 2.2 才能写 2.3，因为引用编号要连号）。本 skill 给每个板块预分配编号段（如 §2.3 = [36-60]，§2.4 = [61-90]），subagent 在自己的段内自由追加，不再依赖执行顺序。Window B 内的竞争格局和市场规模可以**同时出提纲、同时写正文**。

### 差异化审批 —— 高风险板块重点审，低风险板块快速过

| 板块 | 风险 | 审批方式 |
|---|---|---|
| §2.3 竞争格局 | 高 | **先交提纲→批准→写正文→抽检** |
| §2.4 市场规模 | 高 | **先交提纲→批准→写正文→抽检** |
| §2.5 PEST | 低 | 并发派工，快速抽检 |

竞争格局算错 CR3 和市场增速拍脑袋，影响完全不同——审批力度也应该不同。

### 硬科技模式 —— 自动识别 + 学术论文增强

默认硬科技模式，自动启用 Semantic Scholar API 检索英文学术论文（综述/高引/最新），做完整技术分析（6 大主题）。消费/周期/简单制造行业自动切换为基础技术写法，不做无用功。

### HTML 网页报告 —— Boardroom Print 视觉规范

定稿后一键生成 HTML 网页版报告。固定设计系统（"Boardroom Print"风格）：左侧导航 TOC、KPI 数据卡片、深色/浅色模式切换、打印 A4 适配。依赖 `frontend-design` 技能（Claude Code 内置），单文件零依赖。

### 反幻觉框架 —— 6 条硬规则

| 规则 | 做法 |
|---|---|
| 数字 | 必有引用或公式。无源标 `[UNSOURCED]` |
| 公司案例 | 必有出处（年报/招股书/权威报道） |
| 政策 | 必带文号、发文日期、链接 |
| 国外对标 | 必有检索溯源 |
| 排名/市占率 | 必带年份和统计机构 |
| 趋势词 | 用"过去3年CAGR=X%"代替"快速增长" |

### 中程交互 —— 不只开始和结束才问你

竞品分组逻辑（按商业模式/客户群/出身？）、定位可视化选型（2×2矩阵/雷达图/层级图？）、市场规模测算方法（需求导向/供给导向？）、三情景假设合理性——这些关键判断在中程弹出 AskUserQuestion 让你参与决策，而不是 AI 替你猜。

## 前置条件

1. **Claude Code**（CLI 版）已安装
2. **frontend-design 技能**（Claude Code 内置，需启用）—— 用于生成 HTML 报告
3. **Semantic Scholar API Key**（免费注册）：https://www.semanticscholar.org/product/api
4. **Wind MCP Skill**（推荐，免费）：每日赠送 1000 积分，[安装方法](https://github.com/anthropics/skills/tree/main/skills/wind-mcp-skill)

## 安装

```bash
# 将本目录复制到 Claude Code skills 目录
cp -r 行业研究-skill ~/.claude/skills/行业研究

# 设置 S2 API Key 环境变量（二选一）
# 方式1：~/.bashrc 或 ~/.zshrc
export S2_API_KEY="你的S2_API_KEY"

# 方式2：~/.claude/settings.json
{ "env": { "S2_API_KEY": "你的S2_API_KEY" } }
```

## 使用方法

在 Claude Code 中输入以下任意一句即可触发：

- "研究一下 XX 行业"
- "做一份 XX 行业研究报告"
- "分析 XX 赛道"

触发后 skill 会自动引导你完成四步闸门（行业口径/目的/资料/时间预算），然后进入三段审批流程。

## 报告结构

```
一、行业定义与分类
二、行业生命周期阶段定位
三、产业链与价值链
四、竞争格局
五、市场规模与增长驱动
六、外部因素（PEST）
七、景气度指标体系
附录 A：数据矛盾与不确定性
附录 B：待补充资料清单
附录 C：参考资料
```

## 配套功能

- **`/screen-ideas`**：基于报告结果，运行量化筛选 + 主题映射，输出 5-10 个投资标的短名单
- **`/screen-ideas`**：基于报告结果，运行量化筛选 + 主题映射，输出 5-10 个投资标的短名单

## 文件结构

```
行业研究-skill/
  SKILL.md                           # 主文件
  README.md                          # 本文件
  references/
    pre-flight-gate.md               # 四步闸门详细步骤
    anti-hallucination-rules.md      # 反幻觉硬规则
    citation-format.md               # 引用格式规范
    data-sources.md                  # 数据源清单
    report-skeleton.md               # 报告骨架
    html-report-design.md            # HTML 报告视觉规范
  skills/
    industry-definition/SKILL.md     # §2.0+2.1 行业定义与生命周期
    supply-chain/SKILL.md            # §2.2 产业链与价值链
    competitive-landscape/SKILL.md   # §2.3 竞争格局
    market-sizing/SKILL.md           # §2.4 市场规模
    pest-analysis/SKILL.md           # §2.5 PEST
    sentiment-indicators/SKILL.md    # §2.6 景气度
    idea-screening/SKILL.md          # 可选：标的筛选
  commands/
    screen-ideas.md                  # /screen-ideas 命令
```

## License

MIT
