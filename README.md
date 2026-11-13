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
- 「把『燈塔看守人半夜聽見敲門聲』拍成 3 分鐘短片,先列每一步要多少點」
- 「幫我審《雨夜》這個專案的片,列出要重拍的」
- 「幫我健檢這場戲的結構」(貼上劇本)
- 「這批十個鏡頭總共要多少點?」
- 「用這張照片和這段錄音做 30 秒的口播」
- 「這支片把右邊桌上的紅色馬克杯拿掉」(附上影片)
- 「替這支短片做一首台語片尾曲,溫暖的手風琴,先報價」

| 你想做的事 | Claude 會怎麼做 |
| --- | --- |
| 拍影片 / 出圖 / 配音 | 先照你的習慣排預設、編好提示詞、報價,你確認後才送出 |
| 固定角色的臉 | 從你的定妝庫拿主圖或造型圖當參考 |
| 審片 | 逐顆看過畫面,標出雙胞胎、外觀漂移、方位互換等問題,給你重拍清單 |
| 劇本健檢 | 找出每場戲最弱的地方和最小的修法,你同意才改寫 |
| 報價 | 一批影片、圖片、聲音先算總價,跟實際扣點一致 |
| 讓照片裡的人開口 | 一張人像加一段音訊,做口播、講解、唱歌(大師數字替身) |
| 做歌與配樂 | 用 Suno 一次生兩首;歌名、曲風、歌詞可以先寫好給你看過再送,或只給一句描述讓 Suno 自己寫;先報價再生成 |
| 一部短片從一句話拍到成片 | 寫劇本、拆鏡、從劇本建角色 / 場景 / 道具卡並出圖、逐段開拍、審片、每段定出片版、接成一支片;開始前先把每一步的進度與點數列給你看 |
| 短片 / 短劇直接開拍 | 拆好分鏡、組好提示詞,在專案的某一段或短劇的某一集上直接拍,成片接得上審片與剪輯 |
| 後製改片 | 上傳一支影片(或挑拍好的作品),移除物體、換東西、加特效、換風格、改台詞;依你要改的事挑 Seedance 2.5、可靈 4.0 或萬相 3.0,先報價,改完前後比對給你看 |
| 先拍樣片、再出 1080p | 480p 樣片看過、選定了,再升 1080p;標了 OK 的幾條可以一起轉 |
| 整理與交片 | 同一部戲一個工作區、每場一個資料夾;整部片照每段的出片版接成一支 MP4;要進剪輯軟體,可以匯出時間軸,或把標了 OK 的鏡頭直接排成一條 |

選單裡也有幾個一鍵流程:**整套拍攝流程**、**拍一場戲**、**後製改片**、**導演審片**、**劇本健檢**。

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
