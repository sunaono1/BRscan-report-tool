# BRscan报告整理工具

扫描报告读取与整理的**独立工具**（自 BRscan 工具链的报告读取模块抽出）：读 扫描报告 xlsx + 同名 html 的扫描开关，一键整理成结构化摘要 markdown。不依赖测评项对应表等任何内部数据源，拿上报告就能跑。

> 仓库名受 GitHub 限制为 ASCII（BRscan-report-tool），工具本名 **BRscan报告整理工具**（文件名即 `BRscan报告整理工具.py`）。

## 快速上手

```bash
pip install openpyxl
python3 BRscan报告整理工具.py <报告.xlsx>
```

输出默认落报告同目录 `报告整理_<时间戳>.md`。常用选项：

```bash
python3 BRscan报告整理工具.py <报告.xlsx> --out 摘要.md --top 50
# --html PATH   指定同名 html（默认自动找同目录同前缀 *.html，读口令/漏洞扫描开关）
# --out PATH    摘要 markdown 输出路径
# --top N       每类明细最多列 N 行（默认 20，超出截断并标注总数）
```

## 摘要九段结构

| # | 段 | 内容 |
|---|---|---|
| 1 | 概览统计 | 存活主机 / 开放端口 / PoC 命中 / 弱口令命中 / 组件指纹五项计数 |
| 2 | 扫描开关 | 口令扫描、漏洞扫描开没开；开关关了会注明「无覆盖 ≠ 无问题」 |
| 3 | 存活主机 | 主机清单 |
| 4 | 开放端口（按主机） | 每台端口数 + 端口/指纹，按端口数倒序 |
| 5 | 高危与管理端口提示 | 高危服务 / 明文管理协议 / 带外管理口（整理提示，非测评结论） |
| 6 | Web 站点清单 | 站点 / 端口 / 指纹 |
| 7 | PoC 命中 | URL / PoC 名 / 描述表格 |
| 8 | 弱口令统计 | 只出「端口/模式 → 命中数」统计表 |
| 9 | 组件指纹清单 | 去重指纹 |

## 纪律

- 弱口令段**只记命中数量与服务类型/模式，不抄用户/密码明文**；
- 摘要结尾自带「命中 ≠ 最终判定，需人工复核」注记——工具整理结果须人工复核后方可入核查表与报告。

## 作为模块复用

报告读取层（`load_report` / `load_flags`）可单独 import：

```python
from BRscan报告整理工具 import load_report, load_flags

data = load_report("report.xlsx")      # {"ports", "alives", "vulns", "cracks"}
crack_on, poc_on = load_flags("report.html")   # (口令扫描开, 漏洞扫描开)，未标注为 None
```

弱口令资产表列（序号 主机 端口 用户 密码 模式）只读 主机/端口/模式 三列，用户/密码列在读取层就不进内存。

## 依赖

- Python 3.9+，`openpyxl`

## License

MIT
