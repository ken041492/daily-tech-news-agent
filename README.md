# 🤖 Discord-Driven Dynamic Tech News Orchestrator
*An event-driven daily tech news pipeline built with n8n, powered by local Qwen 2.5:7b for cognitive reasoning and multi-tenant isolation.*

## 🚀 系統核心亮點 (Key Features)
- **事件驅動路由 (Event-Driven Routing)**: 透過 Discord Trigger 接收並回應使用者指令，實現即時自動化管線觸發。
- **確定性數據清洗 (Deterministic ETL)**: 將 RSS 異步爬蟲獲取的資料進行陣列聚合 (Aggregate) 與 JavaScript 字串轉譯，降低 AI 處理負擔並防止幻覺 (Hallucination)。
- **多租戶狀態隔離 (Multi-tenant Isolation)**: 利用 Discord `authorId` 綁定 Simple Memory，實現不同使用者的獨立會話隔離。
- **本地化知識與推理 (Local LLM & RAG)**: 部署於本地端的 Ollama (Qwen 2.5:7b) 模型，結合 Wikipedia Tool 擴充外部知識，並針對台灣本土繁體中文與科技術語進行最佳化映射。
- **視覺化推播 (Rich Discord Embeds)**: 最終結果以精美的 Discord Embeds 視覺化卡片格式推播至頻道。

## 📂 專案目錄結構 (Project Structure)
```text
daily-tech-news-agent/
├── docs/
│   ├── homework4_report.pdf (書面報告)
│   └── screenshots/ (系統運行與架構截圖)
│       ├── agent_topology.png
│       ├── AI_Agent_System_Prompt.png
│       ├── discord_output1.png
│       ├── discord_output2.png
│       ├── discord_output3.png
│       └── n8n_architecture.png
├── workflow/
│   └── daily_tech_news_orchestration.json (n8n 工作流匯出檔)
├── .gitignore
└── README.md
```

## 🧠 n8n 工作流邏輯解析 (Workflow Logic)
本專案資料管線的設計邏輯分為以下 4 個主要階段：
1. **觸發與動態路由 (Trigger & Routing)**：Discord Trigger 攔截使用者訊息，擷取 `authorId` 與指令。透過 Switch 節點的條件規則，將如 `!半導體` 等指令路由至特定主題的 RSS 節點群。
2. **批次聚合與清洗 (Batch ETL Pipeline)**：為避免 LLM 因龐大的非結構化 JSON 陣列產生幻覺 (Hallucination) 或反序列化失敗，系統先透過 `Aggregate` 節點批次攔截 40+ 篇新聞，再透過 `Code (JavaScript)` 節點將關鍵欄位萃取並平坦化為純文字字串上下文。
3. **認知與記憶中樞 (Cognitive & Memory Core)**：`AI Agent` 節點以 Qwen 2.5:7b 為推理大腦，利用 ReAct 框架決定是否觸發 `Wikipedia Tool` 擴充領域知識；同時透過 `Simple Memory` 綁定 Discord `authorId` 變數，實現多租戶狀態隔離 (Multi-tenant State Isolation)。
4. **呈現層解耦 (Presentation Decoupling)**：將 AI 產出的結構化 Markdown 文本，動態對映至 `Discord Send Message` 的 Embeds 卡片屬性中，達成資料處理與 UI 呈現的完美解耦。

## 💬 AI Agent 系統提示詞 (System Prompt)
本專案透過嚴格的 System Prompt 設計，實現了反幻覺、工具強制調用、以及台灣本土化語境的精準輸出：

```text
你是一位資深科技分析師。以下是今天最新的科技新聞原始資料：

{{ $json.text }}

【任務與限制】
1. 請根據上述「真實且精確的資料」內容，撰寫一份專業的情報日報。當閱讀新聞時，遇到你不熟悉的專業名詞（例如新興技術），你必須先使用 中文 Wikipedia 工具搜尋其定義，並將搜尋到的背景知識融入日報的『延伸影響』分析中！請絕對不要編造！
2. ⚠️【字體與術語硬性規範】：你必須全程使用「台灣繁體中文（zh-TW）」輸出，嚴禁出現任何一個簡體字。
3. ⚠️【台灣科技術語轉換】：如果內文涉及科技術語，必須嚴格使用台灣科技業習慣用語。例如：
   * 嚴禁寫「平臺」，必須寫「平台」
   * 嚴禁寫「網絡」，必須寫「網路」
   * 嚴禁寫「數據」，必須寫「資料」
   * 嚴禁寫「人工智能」，必須寫「人工智慧」
   * 嚴禁寫「軟件/硬件」，必須寫「軟體/硬體」
違反以上任何一項規則，整份報告將被視為失敗。

【輸出格式規範】
請嚴格依照以下格式輸出（使用 Markdown 與 Emoji 排版）：

📅 【[動態主題] 情報日報｜今日板塊總覽】
(⚠️ 指令：請根據你收到的新聞內容屬性，自動將上方的 [動態主題] 替換成合適的詞彙，例如：半導體、金融財報、IT技術、綜合科技...等)

🔥 一、 重點新聞深度解析（請選出 3 篇最具產業影響力的文章進行分析）
1. 🚀 **[填入新聞標題]**：
   * **核心亮點**：(根據內容簡述技術突破或事件重點)
   * **延伸影響**：(分析這件事對產業圈的影響)
   * 🔗 原文連結：[點此閱讀](填入link)
(第2、3篇以此類推...)

🔍 二、 新聞快速掃描（將其他重要的文章條列）
* 🔗 [填入標題](填入link) - (用一句話總結)
* 🔗 [填入標題](填入link) - (用一句話總結)

🎯 三、 分析師今日硬核觀點
(請站在產業制高點，根據今天的全部新聞，給出一句極具前瞻性的總結。)
```

## 🛠️ 本地部署與重現指南 (Deployment Guide)
### 前提條件 (Prerequisites)
1. **Ollama**: 安裝 Ollama 並執行 `ollama run qwen2.5:7b` 拉取並啟動模型。
2. **n8n**: 啟動本地端 n8n 服務。
3. **Discord Bot**: 準備一組 Discord Bot Token，並將機器人邀請至您的伺服器。

### 部署步驟 (Step-by-Step)
1. **Clone 專案**:
   ```bash
   git clone https://github.com/ken041492/daily-tech-news-agent.git
   cd daily-tech-news-agent
   ```
2. **匯入工作流**:
   打開 n8n 介面，選擇右上角的 "Import from file"，並匯入 `workflow/daily_tech_news_orchestration.json`。
3. **設定憑證 (Credentials)**:
   - 設定 **Discord Trigger** 與 **Discord Send Message** 節點的 Bot Token 憑證。
   - 確認 **Ollama** 節點連線至您的本地位址 (預設為 `http://localhost:11434`)。
4. **啟用管線**:
   確認設定無誤後，點擊右上角的 "Save"，並將右上角的 toggle 切換為 **Active** 啟用工作流。
