# API 中转倍率计算器

一个本地可用的 API 中转站价格比较工具，用来把复杂的“分组倍率、充值比例、输入/缓存/输出单价”统一换算成相对官方 API 价格的倍率。

## 功能

- 计算输入、缓存、输出三项分项倍率
- 支持人民币充值比例，例如 `1 元买 100 刀`、`240 元买 15000 刀`
- 支持输入、缓存、输出不同单价
- 支持 OpenAI、Claude、Gemini 常见模型官方价预设
- 可从 [LiteLLM 模型价格库](https://github.com/BerriAI/litellm/blob/main/model_prices_and_context_window.json) 在线获取并搜索 2000+ 个模型价格
- 支持填入真实 token 用量，计算加权整体倍率、人民币花费、缓存命中率
- 显示输入、缓存、输出各自每 1M tokens 的人民币花费
- 显示按当前结构加权后的每 1M tokens 人民币花费
- 未填真实 token 时，使用大众估算结构展示参考倍率，并在三项倍率不一致时标注“估算”
- 单文件 HTML，无需后端、无需安装依赖

## 使用方式

在线打开：

[打开 API 中转倍率计算器](https://raw.githack.com/puppnn/api-rate-calculator/main/api-rate-calculator.html)

或者用浏览器打开本地文件：

```text
api-rate-calculator.html
```

## 在线模型价格

页面会自动获取 LiteLLM 的模型价格表。在“LiteLLM 在线价格库”中搜索模型 ID 或供应商，选择模型后点击“应用价格”，即可把价格换算为每 1M tokens 并填入官方基准。

- 输入价使用 `input_cost_per_token`
- 缓存读取价使用 `cache_read_input_token_cost`
- 输出价使用 `output_cost_per_token`
- LiteLLM 未提供缓存读取价时，计算器会提示并暂按普通输入价处理
- 模型包含长上下文阶梯价时会提示；在线选择默认应用基础档价格
- 获取失败时会使用上次成功缓存的数据；没有缓存时仍可使用内置预设

## 默认参数

默认界面参数是：

```text
分组倍率: 5
充值人民币: 1
得到刀余额: 100
中转单价: 5 / 0.5 / 30
官方基准: 5 / 0.5 / 30
实际 tokens: 关闭
```

此时三项分项倍率一致，整体倍率为：

```text
0.050000x 官方价
```

## 计算逻辑

分项倍率：

```text
分项倍率 = 分组倍率 × 中转单价 / 官方单价 / 每元可买刀数
```

开启实际 tokens 后：

```text
官方基础成本 =
  输入 tokens × 官方输入价
+ 缓存 tokens × 官方缓存价
+ 输出 tokens × 官方输出价

中转余额成本 =
  输入 tokens × 中转输入价
+ 缓存 tokens × 中转缓存价
+ 输出 tokens × 中转输出价

实际人民币花费 =
  中转余额成本 × 分组倍率 / 每元可买刀数

加权整体倍率 =
  实际人民币花费 / 官方基础成本
```

缓存命中率：

```text
缓存命中率 = 缓存 tokens / (非缓存输入 tokens + 缓存 tokens)
```

## 注意

“倍率”是相对官方价的比较，不等于绝对花费。缓存命中率更高通常会降低绝对成本，但如果中转缓存分项倍率高于输入分项倍率，加权倍率可能会上升。

在线模型价格来自 LiteLLM 社区价格库，内置预设整理于 `2026-05-14`。大额使用前建议再次核对官方价格：

- [OpenAI API Pricing](https://openai.com/api/pricing/)
- [Anthropic API Pricing](https://www.anthropic.com/pricing#anthropic-api)
- [Google Gemini API Pricing](https://ai.google.dev/gemini-api/docs/pricing)
