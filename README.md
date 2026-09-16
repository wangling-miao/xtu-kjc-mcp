# 湘潭大学科技处 MCP

Cloudflare Worker 上的远程 MCP，用于检索湘潭大学科技处官网。

- 官网：https://kjc.xtu.edu.cn/
- 官方搜索：`/search.jsp?wbtreeid=1001`
- 首次检索：POST
- 翻页：GET `currentnum + newskeycode2`
- `searchScope=0`
- 搜索关键词使用 UTF-8 Base64
- 不需要浏览器 `JSESSIONID`

## 工具

| 工具 | 作用 |
|---|---|
| `search_kjc` | 搜索基金、项目申报、科研通知、验收、公示、政策等 |
| `get_article` | 获取科技处文章正文和附件 |
| `list_sections` | 查看科技处常用栏目 |

### 搜索结果

已适配官网当前 `ul.listg2412` 结构，同时解析：

- 标题
- 日期/时间
- 摘要
- URL
- 当前页
- 总页数
- 总条数

总条数兼容科技处搜索结果中的千位分隔符，例如 `1,497`。

## 部署

```bash
npm i
npx wrangler login
npx wrangler deploy
```

## MCP 地址

```text
https://xtu-kjc-mcp.<你的账号>.workers.dev/61c99d39-e3b5-4ebb-9d55-106c056b1b9f/mcp
```

## 客户端配置

```json
{
  "mcpServers": {
    "xtu-kjc": {
      "type": "http",
      "url": "https://xtu-kjc-mcp.<你的账号>.workers.dev/61c99d39-e3b5-4ebb-9d55-106c056b1b9f/mcp"
    }
  }
}
```

## 自测

```bash
UUID=61c99d39-e3b5-4ebb-9d55-106c056b1b9f
BASE=https://xtu-kjc-mcp.<你的账号>.workers.dev

curl -s "$BASE/health"

curl -s "$BASE/$UUID/mcp" \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-03-26","capabilities":{},"clientInfo":{"name":"curl","version":"0"}}}'
```

搜索“基金”：

```bash
curl -s "$BASE/$UUID/mcp" \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"search_kjc","arguments":{"query":"基金","page":1}}}'
```
