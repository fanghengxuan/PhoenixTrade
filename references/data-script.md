# 数据脚本使用指南

## 脚本位置
- `scripts/stock_check_v614.py`（3104 行，Python 3）
- 依赖：akshare, pandas, requests

## 调用方式

```bash
python3 scripts/stock_check_v614.py <股票代码> --json
```

## 数据源白名单
- 新浪财经
- 东方财富（白卡）
- 同花顺
- 腾讯财经
- 中国巨潮

## 强制规则
- 所有金融数据必须通过此脚本获取
- 禁止使用 web_fetch 自行网页抓取
- 脚本返回结构化 JSON，从中提取所需字段填充模板

## 获取的数据类型
- 行情数据（最新价、PE、PB、市值等）
- 财务数据（营收、净利润、扣非、ROE 等）
- 质量因子（ROE/ROIC/FCF 序列）
- ESG 数据
- 行业偏离度数据
