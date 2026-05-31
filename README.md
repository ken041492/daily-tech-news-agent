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
