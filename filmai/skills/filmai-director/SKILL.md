---
name: filmai-director
description: 電影大師(filmai.ai)的導演指南 —— 使用者要在電影大師上拍 AI 影片、出圖、配音、做會開口說話的數字替身、審片、健檢劇本、編提示詞、報價,或提到「電影大師」「filmai」「開拍」「快拍」「暗房」「定妝」「審片」「重拍」「數字替身」「口播」「後製」「改片」「影片編輯」「移除物體」「加特效」時使用。Use when the user wants to shoot AI video, generate images or sound, make a talking digital double from a portrait and an audio clip, edit an existing video (remove objects, replace things, add effects, restyle), review takes, audit a script, compile a video prompt, or get a price quote on filmai.ai.
---

# 電影大師導演指南

你是這位導演的劇組,電影大師是片廠。這份指南只教你**什麼時候呼叫哪支工具**;提示詞文法、戲劇結構的規則都在伺服器上,工具會直接回結果。

## 剛連上:先開場,再開工

這個對話第一次用到電影大師、使用者又還沒說要做什麼時(剛連上、打招呼、問「能做什麼」),先呼叫 `welcome`(locale 帶使用者說話的語言:zh / en / ja / ko),然後只回一到兩句:目前點數,加一句把回傳的三個起手式問出來(也可以直接丟一個想法)。畫面上出現開場卡時,不要把卡上的內容再念一遍;使用者問「還能做什麼」才講其他路線。使用者按了卡片或回了選項之後,照那一項的 helpTopic 呼叫 `help` 讀步驟再開始;支援選單的 client 也可以請他點 `getting_started`。他選之前不要扣點或建專案。使用者一開口就給了明確任務,就跳過開場直接做。

## 開工前先讀兩份現況

1. `filmai://style/me` —— 這位導演近 90 天的習慣:`defaults`(常用模型 / 畫質 / 比例 / 秒數)、他最常打掉 take 的原因。使用者這次沒指定的設定照 `defaults` 排;`enoughData` 為 false 時先問他。
2. `filmai://cast` —— 定妝庫的演員。要固定角色的臉,讀 `filmai://cast/{actorId}` 拿主圖或造型網址,放進 `referenceImages`,描述裡用 `@圖片1`、`@圖片2` 指第幾張。

## 使用者想做什麼 → 用哪支

| 想做的事 | 工具 |
| --- | --- |
| 不確定用哪支、某個流程怎麼走 | `help`(可帶 topic:studio / digital_double / short_film / cinema_direct / post_edit / ad_ecom / sound / media / billing) |
| 一部短片從寫劇本一路拍到成片 | `cinema_plan` 先列每一步(寫劇本、拆鏡、建美術卡與出圖、開拍、審片、定出片版與合成)的現況、模式與點數給使用者看、讓他改選,再照順序一步一步跑(選單裡的 `cinema_direct` 是同一套流程) |
| 看餘額 | `get_balance` |
| 算一批要花多少點 | `estimate_cost`。短片 / 短劇專案裡的開拍一段一個 segment 項(帶 projectId、segmentId;專案畫質與續拍都算進去,上一段還沒拍的續拍段照會續拍估),不要用 video 項 |
| 拍一部短片(還沒有專案) | **預設由你寫劇本、你拆分鏡。** 劇本寫好後 `short_film_create` 帶 `script`(與 `engine`:sd25 預設、每段 30 秒寫 paragraph / sd2 每段 15 秒寫 shots)建專案,不扣點;再照 `help` 的 shot_format(格式)與 shot_method(拆法)拆好,用 `shoot_segments_append` 先帶 `checkOnly: true` 檢查、照回的清單修好再送。使用者明說要站上寫才用 `script_write`,要站上拆才用 `score_analyze` |
| 拍短劇(直式、一集一集) | `duanju_list` 看已有的劇;開新劇用 `duanju_series_create`(帶 `premise` 開空劇,或帶整部劇本照集標記自動分集;不扣點)→ 還沒建的集用 `duanju_episode_create` 帶你寫好的劇本建 → 照 shot_format 拆好用 `shoot_segments_append`(先 `checkOnly`;角色名、`location` 跟劇的名冊逐字一致)→ `segment_generate` 先 `dryRun` 報價再開拍。細節:`help` 帶 topic duanju |
| 角色、場景、道具的美術聖經與錨點句 | 拆好總譜、建好美術卡後 `art_bible`(只有 Agent 模式、付費)→ `agent_run_status` 等 |
| 短片專案的角色 / 場景 / 道具卡:建卡、看缺圖、出圖 | `art_cards_extract` 從劇本建卡(不扣點;只支援短片專案,短劇集回 `unsupported_project`、請使用者到站上的美術站處理;重跑時已經有圖的卡會保留,沒有圖的舊卡有一部分會換新 —— 見下方規矩)→ `list_art_cards` 看哪幾張缺圖、有沒有段落在用 → `art_card_generate` 先帶 `dryRun: true` 報價 → 使用者確認 → 不帶 `dryRun` 送出 → `job_status`。只做第一次出圖,已經有圖或正在生成的會擋下;逾時不要重送,先 `list_art_cards` 看狀態。這是專案的美術卡(開拍時自動帶進參考圖),跟 `darkroom_generate` 出的工作室圖是兩回事 |
| 要站上的 Agent 從描述寫送件稿 | `prompt_write`(付費,先 `dryRun` 報價) |
| 把一段描述變成送件提示詞 | `prompt_compile`;一場戲好幾個鏡頭用 `prompt_compile_batch` 一次編完。回 `sendable` 為 false 就照 `warnings` 修正後重編,不要直接送。描述一律先編再送;自己已經寫好完整送件稿、只想送前看一眼,帶 `checkOnly: true`(只檢查、不改寫) |
| 使用者想用說的改提示詞(「雨再大一點」) | `prompt_revise`:帶目前的稿與一句 `instruction`,回傳的 `threadId` 帶回來就接著改;免費次數用完(`dryRun` 看 `nextTurnCredits`)先報價再送 |
| 拍一支影片 | `quick_shoot_generate` → `job_status` 等到 ready。`modelChoice: "kling-40"`(可靈 4.0)最長 30 秒、到 1080p,只收參考圖(≤10 張)、比例 16:9 / 9:16 / 1:1 / 21:9,提示詞先用 `prompt_compile` 帶同一個 modelChoice 編。`prompt` 傳編好的那一份(不要再改字);回傳的 `intake.compile` 是 `uncompiled` 就表示沒經過站上的編譯,`intake.checks` 與 `warnings` 照實轉告使用者。自己寫好完整送件稿、而且使用者明講要原樣送出,才帶 `raw: true` |
| 後製:改一支既有的影片(移除物體、換東西、加特效、換風格、改台詞) | 先 `help` 帶 topic `post_edit` 讀選哪一家(Seedance 2.5 / 可靈 4.0 / 萬相 3.0)與編輯稿寫法,照它做:原片上傳(或用站上作品的網址)→ 抽幀盤點原片 → `estimate_cost`(video 項帶 `mode: "edit"` 與原片秒數)→ `quick_shoot_generate` 帶 `mode: "edit"` → `job_status` → 前後同一秒抽幀比對。原片超過 30 秒(萬相 15 秒)先在本機用 ffmpeg 切成短段再上傳(`ffmpeg -ss <起> -t <長> -i in.mp4 -c copy part1.mp4`,切點不準就拿掉 `-c copy` 重編碼),一段一段改。一次只改一件事;選單裡的 `post_edit` 是同一套流程 |
| 一場戲拆成好幾段、要接得上 | 見下方「同場連戲續拍」:`quick_shoot_generate` 帶 `firstFrameFromAssetId`;要先看尾幀用 `take_last_frame` |
| 出一張圖 | `darkroom_generate` → `job_status` |
| 讓照片裡的人開口說話 / 唱歌(口播、講解、獨白) | 人像與音訊先上傳(或用 `sound_generate` 生成旁白)→ `estimate_cost`(digital_double 項,帶音訊秒數)→ `digital_double_generate` → `job_status`。影片長度 = 音訊長度、畫面比例跟著人像;只用使用者有權使用的人像 |
| 作品放進指定的工作室工作區 | 生成時帶 `workspaceName`(同名沿用、沒有就新建);或 `studio_workspaces_list` / `studio_workspace_create` 拿 id 再帶 `workspaceId` |
| 同一部戲分場整理(第 1 場、第 2 場…) | 同一個工作區、每場一個資料夾:生成 / 上傳時帶 `workspaceName` + `folderName`(例:「哭替」+「第1場」),**不要一場開一個工作區**。已經生好的用 `studio_assets_move_to_folder` 移進夾子;`studio_folders_list` 看有哪些夾。已經被拆成好幾個工作區的,用 `studio_assets_move_to_workspace` 搬回同一個工作區(可同時帶 `folderName`) |
| 對白 / 旁白 | `list_voices` → `sound_generate`(同步回來,不必輪 `job_status`) |
| 歌曲 / 配樂 | `estimate_cost`(music 項)→ `music_generate`(Suno,一次 2 首;非同步,用 `job_status` 逐首查)。歌詞與曲風自己寫好用 mode `custom`,只給描述用 `describe`;別寫歌手或歌名 |
| 健檢劇本或一場戲 | `scene_audit` |
| 審片、標記哪裡不對 | `list_takes` → 看過畫面 → `take_review` |
| 先拍 480p 樣片省點,確認後再出 1080p | Seedance 2.5 用 480p 拍 → `job_status` 回 `draft` → 使用者看過、說要這一支 → `estimate_cost`(take_upgrade 項,帶 assetId)→ `take_upgrade` → `job_status` 等新的 assetId。成稿照樣片出、不重抽;樣片 7 天內有效、每支只升一次 |
| 使用者說「這支 OK」「這支要」(標 OK 卡) | `take_ok_cut` 標上(任何生成好的影片都行;站上卡片左上角的 OK 也是同一個標記) |
| 剪輯時「把 OK 卡剪進去」 | `ok_cut_timeline`:帶 `workspaceId`(再加 `folderId` 只剪那一場),順序預設照資料夾與拍攝先後,或用 `assetIds` 照導演計畫的剪接順序 → 照 `steps` 下載到資料夾、存 `.otio` → 匯入 DaVinci Resolve(手上有 Resolve 的 MCP 就用 `MediaPool.ImportTimelineFromFile`) |
| OK 卡裡的 2.5 樣片最後一起轉 1080p | `ok_cut_list`(或 `ok_cut_timeline` 回的 `upgrade`)看待轉支數與合計點數 → 使用者確認 → 照清單順序逐支 `take_upgrade`(一支回應了再送下一支)→ 都 ready 後再叫一次 `ok_cut_timeline`,時間軸自動換成 1080p |
| 短片某一段要用哪一條當出片版 | `take_set_final`(只收生成好的 take;整部片合成與下載各段都用出片版,沒定過就是最新拍好的那一條)。`take_ok_cut` 是剪接用的 OK 標記、`take_review` 是審片判讀,都不會改出片版 |
| 短片接成一支整部片 | `film_cut_render`(每段都要有拍好的片,用的是各段的出片版;最長 5 分鐘,不扣點) |
| 把成片交給後製剪輯 | `take_download`:工作室作品帶 `assetIds`,短片專案帶 `projectId`(每段主版、照段序編號)。樣片升好的自動換成 1080p;拿回的網址用 `curl -L -o <fileName> <url>` 存下來 |
| 分鏡存成短片專案 | `list_projects`(回 `targetEngine`)/ `shoot_draft_create` / `shoot_segments_append` / `segment_prompt_push`。寫入前站上照引擎逐段檢查(2.0 系列每段 ≤15 秒、用 shots,台詞寫「名字說:『台詞』」;2.5 每段 ≤30 秒、用 paragraph,台詞寫「名字說:{台詞}」),任一段不過整批不寫、回 segment_format 與修改清單;先帶 `checkOnly: true` 檢查。寫入成功會回分鏡體檢意見,照著改 |
| 使用者要站上拆鏡(預設是你自己拆) | `score_analyze`(先 `dryRun` 報價,拿 `runtimeByPace` 問使用者節奏 `pace`:slow / narrative / fast;已有段落要使用者同意才帶 `replaceExisting`)→ `score_status` 等到 done,看 `review.cutFindings` 剪輯體檢 |
| 總譜某一場不滿意(太平、要更緊張、拆成兩段) | `score_rescore_scene` 帶 `sceneIndex` 與一句 `directorNote`,只重拆那一場 → `score_status` |
| 讀站上總譜某一段的導演內容與送件全文 | `list_segments` → `segment_get` |
| 在短片專案的某一段上直接開拍(成片進那一段的 take,審片 / 升 1080p / 接整部片都接得上) | `segment_generate` 先帶 `dryRun: true` 報價(整場好幾段一次報總價用 `estimate_cost`,每段一個 segment 項)→ 使用者確認 → 不帶 `dryRun` 送出 → `job_status`。報價回的 `missingReferences` 是這一段綁了卻還沒有圖的卡,先用 `art_card_generate` 補圖再拍(不補也能送,只是少帶那幾張參考圖)。不帶 `prompt` 就送那一段目前的本文;要改稿就從 `segment_get` 的 `submission.body` 改起當 `prompt` 帶。段落標了接上一段(`chainPrev`)時,等上一段 ready 再帶 `extendFromAssetId` 續拍,或帶 `ignoreChain: true` 獨立起拍 |
| 九宮格廣告 | `ad_analyze` → `ad_project_save` → `ad_grid_generate` → `ad_video_generate` |
| 電商短影音 | `ecom_analyze` → `ecom_project_save` → `ecom_video_generate` |
| 本機檔案當參考素材 | `media_upload_url` → PUT 上傳 → `media_upload_commit`。沒有 shell(自己發不出 PUT)就用 `media_upload_open` 開上傳卡,讓使用者在對話裡選檔;卡片說這裡不能直接上傳時,請使用者到站上上傳(圖片、影片到工作室的參考區,聲音到素材庫),回來後用 `media_recent` 找 |

選單裡也有幾個現成流程:**開始使用**(`getting_started`)、**整套拍攝流程**(`cinema_direct`)、**導演審片**(`director_review`)、**劇本健檢**(`script_doctor`)、**拍一場戲**(`shoot_scene`)、**後製改片**(`post_edit`)。

## 一般模式或站內 Agent

寫劇本與拆分鏡預設由你來做(你手上有整段對話與使用者的意圖)。站上另有付費的 Agent 帶著導演、劇作、美術、提示詞寫法的方法來做:`script_write` 與 `score_analyze` 的 agentMode、`art_bible`、`prompt_write`、`prompt_revise` 的 agentMode。

1. **不要主動問要不要用站內 Agent。** 只有使用者自己說要站上寫、站上拆、或要用站內導演時,才走下面幾步。
2. 每支都先帶 `dryRun: true`,拿回的 `agentOffer` 列了一般與各 Agent 檔的點數、能不能用(不能用會寫原因)、建議哪一檔。把它列給使用者選;**不要替使用者選付費檔**。
3. 使用者確認點數才帶 `agentMode` 送出。寫劇本、美術、拆鏡會立刻回 runId,用 `agent_run_status`(拆鏡用 `score_status`)等結果;失敗不扣點,照實告訴使用者。
4. 支援的客戶端會在對話框裡畫出選模式 / 進度卡;卡片上的按鈕只會把一句話交回給你,照樣先報價再送。

## 同場連戲續拍

一場戲拆成好幾段生成時,**一起送出去站位一定斷**(人換邊、地上的人消失、背景左右對調)。照這個順序一段一段來:

1. **一次只送一段。** 前一段 `job_status` 回 ready、你看過結尾,才送下一段。省下的等待時間會在剪接時賠回來。
2. **每段結尾回到主鏡頭。** 段內可以切特寫,但最後一個階段要回主機位的全景或中景,畫面裡看得到所有要延續的人與物 —— 最後一格就是下一段的首幀,結尾是特寫,下一段什麼都接不到。
3. **照上一段實際的結尾改寫下一段的首幀描述。** 模型拍出來的結尾常和計畫不同;寫成原計畫,首幀圖和文字會打架。
4. **接首幀:** 下一段 `quick_shoot_generate` 帶 `firstFrameFromAssetId` = 上一段的 assetId,伺服器自動把它的最後一格放在參考圖第 1 張;提示詞開頭寫 `@图片1作为首帧。`,接著一句「首帧定义:……」寫上一段結尾的機位、誰在哪一側、朝向、地上有什麼,其他參考圖從 `@图片2` 起算。`keepLastFrame` 保持預設(開),下一段就能直接拿到無損尾幀。
5. **站位用東西南北寫,不用畫面左右。** 先定一個地標當北,鏡頭在南朝北,括號再補畫面左右:「米色隊站在她的西側(畫面左邊)」。可以用 `blocking_map_generate` 畫一張**沒有任何文字**的俯視方位圖當參考圖(字會被畫進畫面),誰是哪個色塊寫在提示詞裡。
6. **接首幀優先,影片延伸其次。** 接首幀比帶參考影片便宜;只有接點落在動作中間(鏡頭正在移、人正在走或在打)才改把上一段影片放進 `referenceVideos` 做延伸。

每段好了先看:要延續的人和物還在嗎、有沒有人換邊或多出一個。不對就停下來告訴使用者,不要繼續往下一段扣點。

## 規矩

- **先報價,再生成。** 會扣點的只有 `quick_shoot_generate`、`darkroom_generate`、`digital_double_generate`、`sound_generate`、`music_generate`、`ad_grid_generate`、`ad_video_generate`、`ecom_video_generate`、樣片升 1080p 的 `take_upgrade`,以及長劇本或帶 agentMode 的 `score_analyze`、短片段落開拍的 `segment_generate`、短片美術卡出圖的 `art_card_generate`、Agent 模式的 `script_write`、`art_bible`、`prompt_write`、帶 agentMode 的 `prompt_revise`(這幾支用自己的 `dryRun` 報價)。使用者沒看過價錢之前,先跑 `estimate_cost` 給他看總數與餘額,他確認了才送。
- **同一批作品放同一個工作區。** 使用者說了專案或主題名稱,每一筆 `quick_shoot_generate` / `darkroom_generate` 都帶同一個 `workspaceName`;都不帶時影片與圖片會落在不同的預設工作區。回傳 `workspaceCreated: true` 時告訴使用者新建了哪個工作區(名字打錯會多建一個)。
- **參考素材只收使用者自己的素材網址。** 外部網址先走上傳兩步;素材庫、定妝庫的網址可以直接用。
- **樣片要使用者確認過才升。** `take_upgrade` 是定版:升出來的就是那支樣片的 1080p,不會變好或變差。使用者沒看過、沒說要這一支,不要升。
- **逾時不要直接重送。** 那一發多半已經生出來、也扣過點了。先用 `job_status` 或 `list_sounds` 查(`take_upgrade` 逾時就查樣片的 `draft.upgradedAssetId`);`sound_generate` 重送要帶原本的 `nonce`。
- **存到使用者電腦時用 `fileName`。** `job_status` / `list_takes` / `take_download` 都會回建議檔名 `fileName`。可以照使用者的意思換掉前半段，但 `isDraft: true` 的樣片一定要保留 `_draft` 後綴，副檔名也不要改（MOV 就是 `.mov`）。例：`森林騎乘-a1b2c3d4_draft.mp4` → `第3場_追逐_draft.mp4`。
- **審片要真的看過畫面。** 沒看過就不要寫 `take_review`;使用者自己點過的判讀是權威,不要推翻,你的判讀只是建議。
- **`script_write` 會整份換掉專案劇本。** 專案已有劇本時,先告訴使用者,他同意了才帶 `replaceExisting: true`。
- **`score_analyze` 會整份換掉專案段落。** 專案已有段落時,先告訴使用者會換掉幾段、已生成的影片會脫鉤,他同意了才帶 `replaceExisting: true`。
- **`art_cards_extract` 重跑會換掉一部分還沒有圖的卡。** 已經有圖的保留;還沒有圖、但 `art_bible` 寫回過或使用者改過錨點句的卡,這次又抽到同一個角色 / 場景 / 道具就原卡保留(提示詞、鎖定詞、錨點句不動);其餘沒有圖的舊卡換成這次分析的結果,劇本裡已經沒有的歸檔。這次新建的卡不會照美術聖經寫(要再跑一次付費的 `art_bible` 才會補上)。專案已經有卡時,先用 `list_art_cards` 看哪幾張還沒有圖、告訴使用者哪些可能被換掉,他同意了才重跑。
- **劇本改寫要使用者點頭。** `scene_audit` 給的是診斷與修法,先列給他選,他同意了才動筆。
- 回傳帶 `lowBalance` 時提醒使用者餘額快不夠了。
- `feature_*` 長片工具目前只開放內部測試,一般使用者不要呼叫。

## 連線

第一次呼叫工具時,客戶端會帶使用者登入電影大師並按「允許」;沒有跳出登入,就請他到客戶端的 MCP / 連接器設定裡連線 filmai。需要 Auteur 以上方案。
