# Haystack x Bright Data 集成

[![PyPI 版本](https://badge.fury.io/py/haystack-brightdata.svg)](https://badge.fury.io/py/haystack-brightdata) [![Python 版本](https://img.shields.io/pypi/pyversions/haystack-brightdata.svg)](https://pypi.org/project/haystack-brightdata/) [![许可证](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

将 Bright Data 强大的网页爬虫和数据提取功能集成到你的 Haystack 流水线中。此软件包提供三个 Haystack 组件，用于：

- 🔍 **搜索引擎 API** - 获取来自 Google、Bing、Yahoo 等搜索引擎的结果
- 🌐 **网络解锁器** - 访问受地理限制和反爬虫保护的网站
- 📊 **网页抓取工具** - 从 43 个以上受支持的网站提取结构化数据

## 功能

- **无缝集成 Haystack** - 原生支持 Haystack 2.0+ 流水线
- **支持 43 个以上的数据集** - 从 Amazon、LinkedIn、Instagram、Facebook、TikTok、YouTube 等平台提取数据
- **地理定位** - 访问特定国家/地区的内容
- **绕过反爬虫机制** - 自动处理 CAPTCHA 和机器人检测
- **结构化数据** - 获取可直接用于 RAG 流水线的干净结构化 JSON 数据
- **异步支持** - 内置异步支持，适用于高性能应用

## 安装

```bash
pip install haystack-brightdata
```

## 快速开始

### 前提条件

1. 从 [https://www.bright.cn/cp/api_access](https://www.bright.cn/cp/api_access) 获取你的 Bright Data API 密钥
2. 设置环境变量：

```bash
export BRIGHT_DATA_API_KEY="your-api-key-here"
```

### 示例 1：SERP 搜索

```python
from haystack_brightdata import BrightDataSERP

# Initialize the component
serp = BrightDataSERP()

# Execute a search
result = serp.run(
    query="Haystack AI framework tutorials",
    num_results=10,
    country="us"
)

print(result["results"])  # Parsed JSON results
```

### 示例 2：网络解锁器

```python
from haystack_brightdata import BrightDataUnlocker

# Initialize the component
unlocker = BrightDataUnlocker()

# Access a restricted website
result = unlocker.run(
    url="https://example.com",
    country="gb",
    output_format="markdown"
)

print(result["content"])  # Clean markdown content
```

### 示例 3：网页爬虫工具

```python
from haystack_brightdata import BrightDataWebScraper

# Initialize the component
scraper = BrightDataWebScraper()

# Extract Amazon product data
result = scraper.run(
    dataset="amazon_product",
    url="https://www.amazon.com/dp/B08N5WRWNW"
)

print(result["data"])  # Structured JSON data
```

### 示例 4：在 Haystack 流水线中使用

```python
from haystack import Pipeline
from haystack_brightdata import BrightDataSERP

# Create a pipeline
pipeline = Pipeline()
pipeline.add_component("search", BrightDataSERP())

# Run the pipeline
result = pipeline.run({
    "search": {
        "query": "Python web scraping",
        "num_results": 20
    }
})

print(result["search"]["results"])
```

## 组件

### BrightDataSERP

跨多个搜索引擎执行搜索查询，并支持地理定位和结果解析。

**参数：**
- `bright_data_api_key` (Optional[str])：API 密钥（默认使用 `BRIGHT_DATA_API_KEY` 环境变量）
- `zone` (str)：Bright Data 区域名称（默认值："serp"）
- `default_search_engine` (str)：默认搜索引擎（默认值："google"）
- `default_country` (str)：默认国家/地区代码（默认值："us"）
- `default_language` (str)：默认语言代码（默认值："en"）
- `default_num_results` (int)：默认结果数量（默认值：10）

**输出：**
- `results` (str)：JSON 字符串格式的搜索结果（当 `parse_results=True` 时，为默认设置）或原始 HTML

### BrightDataUnlocker

访问受地理限制和反爬虫保护的网站，并自动破解验证码。

**参数：**
- `bright_data_api_key` (Optional[str])：API 密钥（默认使用 `BRIGHT_DATA_API_KEY` 环境变量）
- `zone` (str)：Bright Data 区域名称（默认值："unlocker"）
- `default_country` (str)：默认国家/地区代码（默认值："us"）
- `default_output_format` (str)：默认输出格式，html、markdown 或 screenshot（默认值："html"）

**输出：**
- `content` (str)：指定格式的网页内容

### BrightDataWebScraper

从 43 个以上受支持的网站提取结构化数据。

**参数：**
- `bright_data_api_key` (Optional[str])：API 密钥（默认使用 `BRIGHT_DATA_API_KEY` 环境变量）
- `default_include_errors` (bool)：在输出中包含错误（默认值：False）

**输出：**
- `data` (str)：JSON 字符串格式的结构化数据

**辅助方法：**
```python
# Get all supported datasets
datasets = BrightDataWebScraper.get_supported_datasets()

# Get info about a specific dataset
info = BrightDataWebScraper.get_dataset_info("amazon_product")
```

## 支持的数据集（43+）

### 电子商务（10）
- Amazon：商品、评论、搜索结果、畅销商品
- Walmart：商品、卖家
- eBay、Home Depot、Zara、Etsy、Best Buy

### LinkedIn（5）
- 个人资料、公司资料、职位列表、帖子、人员搜索

### 社交媒体（16）
- **Instagram**：个人资料、帖子、Reels、评论
- **Facebook**：帖子、Marketplace、公司评价、活动
- **TikTok**：个人资料、帖子、商店、评论
- **YouTube**：频道资料、视频、评论
- **X/Twitter**：帖子
- **Reddit**：帖子

### 商业智能（2）
- Crunchbase、ZoomInfo

### 搜索与商业（6）
- Google 地图评论、Google Shopping、Google Play 商店
- Apple App Store、Zillow、Booking.com

### 其他（5）
- GitHub、Yahoo Finance、Reuters

[查看完整数据集列表](https://github.com/bright-cn/haystack-brightdata#supported-datasets)

## 高级用法

### 自定义区域配置

```python
serp = BrightDataSERP(zone="my_custom_serp_zone")
```

### 地理定位搜索

```python
result = serp.run(
    query="local restaurants",
    country="fr",  # France
    language="fr",
    num_results=20
)
```

### 多格式网络解锁器

```python
# Get as markdown
markdown = unlocker.run(url="https://example.com", output_format="markdown")

# Get as screenshot
screenshot = unlocker.run(url="https://example.com", output_format="screenshot")
```

### 数据集特定参数

```python
# LinkedIn people search
result = scraper.run(
    dataset="linkedin_people_search",
    url="https://www.linkedin.com",
    first_name="John",
    last_name="Doe"
)

# Google Maps reviews (last 7 days)
result = scraper.run(
    dataset="google_maps_reviews",
    url="https://www.google.com/maps/place/...",
    days_limit="7"
)
```

## 环境变量

- `BRIGHT_DATA_API_KEY` - 你的 Bright Data API 密钥（必需）
- `REQUESTS_CA_BUNDLE` - 用于企业代理的自定义 CA 证书包（可选）
- `SSL_CERT_FILE` - 备用 SSL 证书文件（可选）

## 要求

- Python >= 3.8
- haystack-ai >= 2.0.0
- pydantic >= 2.0.0
- requests >= 2.28.0
- aiohttp >= 3.8.0

## 示例

查看[示例目录](https://github.com/bright-cn/haystack-brightdata/tree/main/examples)，了解更多详细示例：

- `example_serp.py` - 搜索引擎 API 示例
- `example_unlocker.py` - 网络解锁器示例
- `example_scraper.py` - 网页抓取工具示例
- `example_pipeline.py` - 流水线集成示例

## 文档

- [Bright Data API 文档](https://docs.brightdata.com/)
- [Haystack 文档](https://docs.haystack.deepset.ai/)
- [组件 API 参考](https://github.com/bright-cn/haystack-brightdata#api-reference)

## 贡献

欢迎贡献！你可以随时提交拉取请求。

## 许可证

本项目采用 Apache License 2.0 许可，详情请参阅 [LICENSE](LICENSE) 文件。

## 支持

- **问题**：[GitHub Issues](https://github.com/bright-cn/haystack-brightdata/issues)
- **Bright Data 支持**：[support@brightdata.com](mailto:support@brightdata.com)
- **Haystack 社区**：[Haystack Discord](https://discord.gg/haystack)

## 致谢

- 由 [deepset](https://www.deepset.ai/) 为 [Haystack](https://haystack.deepset.ai/) 构建
- 由 [Bright Data](https://www.bright.cn/) 提供支持

---

**注意**：你需要有效的 Bright Data 订阅才能使用此软件包。前往 [brightdata.com](https://www.bright.cn/) 开始使用。
