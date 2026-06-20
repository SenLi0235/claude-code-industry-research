# /screen-ideas — 投资标的筛选

在行业研究报告完成后，基于报告中 §2.2 的代表企业池和 §2.3 的竞争格局对标表，运行量化筛选 + 主题映射，输出 5-10 个标的的短名单与一页摘要。

前置条件：需要一份已完成的行业研究报告（由行业研究 skill 生成）。

用法：
```
/screen-ideas <行业名> [--direction long|short|both] [--style value|growth|quality|special] [--market-cap large|mid|small|all]
```

参数说明：
- `--direction`：做多/做空/两者（默认 both）
- `--style`：价值/成长/质量/特殊事件（默认 all）
- `--market-cap`：大/中/小/不限（默认 all）

执行流程：
1. 读行业研究报告，提取代表企业池和竞品对标表
2. AskUserQuestion 确认筛选参数（如未通过命令行指定）
3. 按风格运行量化筛选，每类筛选标注使用的具体阈值
4. 主题映射（如适用）：区分纯 play vs 间接受益
5. 输出短名单 + 每人一页摘要 + 跨标的对比总表 + 优先级排序

详见 `skills/idea-screening/SKILL.md`。
