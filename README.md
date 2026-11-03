# tacyzo 的 DSH 插件市场索引

这是给 **DeepSeek Harness** 用的插件市场索引源（plugin market index，`format: 1`）。
里面的插件都是我自己的定制版本，通过 git commit 固定版本号。

## 索引地址

```
https://cdn.jsdelivr.net/gh/tacyzo/dsh-plugins@main/index.json
```

备用（jsDelivr 不通时）：

```
https://raw.githubusercontent.com/tacyzo/dsh-plugins/main/index.json
```

## 在 DSH 里添加

侧边栏 → 设置 → 插件 → **发现（Discover）** → **索引源（Index sources）** → 添加 → 粘贴上面的 URL。

加完之后「发现」页就会出现本仓库里的插件，可以直接安装；装了之后市场会显示「可更新」。

缓存是 10 分钟（`cacheTtlMs` 默认 600000），刚推上去的改动等一会儿刷新即可。

## 当前包含的插件

| 包名 | 说明 | 版本 |
|---|---|---|
| `dsh-image2-tool` | 接 grsai 原生接口（`POST /v1/api/generate`）的文生图/图生图，针对 `gpt-image-2.5` 调校 | `0.4.3-grsai.2` |

## 新增一个插件

1. 把插件代码放在一个独立的公开仓库里，比如 `github.com/tacyzo/dsh-xxx`。
2. 确认 `package.json` 里 `name`、`version`（合法 SemVer）、`main` 正确，并且有 `dsh.bundle.patch` 指向 `cordis.patch.yml`。
3. 拿到要做成版本的 **完整 40 位 commit SHA**：
   ```bash
   git rev-parse HEAD
   ```
4. 在 `index.json` 的 `plugins` 数组里追加一项：

```json
{
  "name": "dsh-xxx",
  "title": { "en": "Xxx", "zh": "某某插件" },
  "description": { "en": "...", "zh": "..." },
  "homepage": "https://github.com/tacyzo/dsh-xxx",
  "repository": { "url": "https://github.com/tacyzo/dsh-xxx" },
  "metadata": { "category": "tools", "keywords": ["..."] },
  "versions": [
    {
      "version": "1.0.0",
      "publishTimestamp": 1793714260,
      "source": {
        "type": "git",
        "url": "https://github.com/tacyzo/dsh-xxx",
        "commit": "<40 位 commit SHA>"
      },
      "engines": { "dsh": ">=0.2.1-0 <0.3.0" }
    }
  ]
}
```

5. 推送到 `main`，等缓存过期或直接刷新「发现」页。

## 已装插件更新到新版本

同一插件追加一个 `versions` 元素（新版本 + 新 commit），旧版本保留即可 —— 市场会按 SemVer 降序挑最新的兼容版本。

## 格式要点（别踩坑）

- `format` 必须是数字 `1`。
- `title` 可以是字符串，也可以是 `{ "en": "...", "zh": "..." }`，但**对象形式必须含 `en`**。
- `version` 必须是**精确的 SemVer**（`0.4.3-grsai.2` 合法，`v0.4.3` 不合法）。
- `publishTimestamp` 是**整数 Unix 秒**，不是毫秒。
- `source.type` 只能是 `npm` 或 `git`；`git` 时 `commit` 必须是**小写 40 位十六进制**。
- `url` 必须是规范化的 `https://` 地址，至少两段路径，不能指向 `.tgz`。
- 每个 `name` 在同一个索引里不能重复。

校验方式（本地跑官方校验器）：

```bash
npm i @deepseek-ai/dsh-plugin-market-index@0.2.1-rc.5
node -e "import('@deepseek-ai/dsh-plugin-market-index').then(m=>{const j=JSON.parse(require('fs').readFileSync('index.json','utf8'));console.log(m.parseMarketIndex(j,'https://example.com/index.json'))})"
```

## 许可

本仓库只有索引元数据，`index.json` 与 README 以 CC0 释出；各插件本身的许可见其各自仓库。
