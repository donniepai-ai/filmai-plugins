# 電影大師 filmai.ai — 官方 plugin(Claude Code / Codex)

讓 Claude 或 Codex 當你的劇組,[電影大師](https://filmai.ai) 當你的片廠:在對話裡直接拍 AI 影片、出圖、配音,
審片、健檢劇本,而且**先報價、你確認了才生成**。

## 安裝(Claude Code)

在 Claude Code 裡依序執行:

```
/plugin marketplace add donniepai-ai/filmai-plugins
/plugin install filmai@filmai
/mcp
```

最後一步選 `filmai`,在跳出的頁面登入電影大師並按「允許」就連上了。**不用 API 金鑰。**

> 需要電影大師 Auteur 以上方案。

## 安裝(Codex)

在 Codex app 的 plugin 設定加入 marketplace `https://github.com/donniepai-ai/filmai-plugins.git`,
安裝 `filmai`。第一次用到電影大師的功能時,在跳出的頁面登入並按「允許」。**不用 API 金鑰。**

也可以直接寫進 `~/.codex/config.toml`:

```toml
[marketplaces.filmai]
source_type = "git"
source = "https://github.com/donniepai-ai/filmai-plugins.git"

[plugins."filmai@filmai"]
enabled = true
```

## 裝了之後可以做什麼

直接用中文跟 Claude 說,例如:

- 「用我定妝庫裡的林昊拍一支 10 秒的雨夜追逐,先給我報價」
- 「幫我審《雨夜》這個專案的片,列出要重拍的」
- 「幫我健檢這場戲的結構」(貼上劇本)
- 「這批十個鏡頭總共要多少點?」

| 你想做的事 | Claude 會怎麼做 |
| --- | --- |
| 拍影片 / 出圖 / 配音 | 先照你的習慣排預設、編好提示詞、報價,你確認後才送出 |
| 固定角色的臉 | 從你的定妝庫拿主圖或造型圖當參考 |
| 審片 | 逐顆看過畫面,標出雙胞胎、外觀漂移、方位互換等問題,給你重拍清單 |
| 劇本健檢 | 找出每場戲最弱的地方和最小的修法,你同意才改寫 |
| 報價 | 一批影片、圖片、聲音先算總價,跟實際扣點一致 |

選單裡也有三個一鍵流程:**導演審片**、**劇本健檢**、**拍一場戲**。

## 這個 plugin 裡有什麼

```
.claude-plugin/marketplace.json     marketplace「filmai」(Claude Code)
.agents/plugins/marketplace.json    marketplace「filmai」(Codex)
filmai/
├── .claude-plugin/plugin.json      plugin「filmai」(Claude Code)
├── .codex-plugin/plugin.json       plugin「filmai」(Codex)
├── .mcp.json                       連線到 https://filmai.ai/api/mcp/mcp(OAuth 登入)
├── assets/                         圖示
└── skills/filmai-director/         導演指南:什麼時候用哪個功能、先報價再生成等規矩
mcp-registry/server.json            官方 MCP 目錄的登記檔(ai.filmai/filmai)
```

Claude Code 與 Codex 共用同一份導演指南與連線設定。

## 不用 Claude Code 或 Codex?

- **claude.ai / ChatGPT**:在「連接器」新增自訂連接器,貼上 `https://filmai.ai/api/mcp/mcp`,登入電影大師按「允許」。
- **其他 MCP 客戶端**:電影大師已上架[官方 MCP 目錄](https://registry.modelcontextprotocol.io/v0/servers?search=ai.filmai),名稱 `ai.filmai/filmai`。

## 隱私

你的專案標題、段落與生成結果會經過你使用的 AI 客戶端(Claude、ChatGPT、Codex 等)。
電影大師端的資料處理見[隱私權政策](https://filmai.ai/privacy)(「Agent 連線(MCP)」一段)。
