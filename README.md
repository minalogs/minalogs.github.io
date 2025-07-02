# Note for Clarity — Mina 的個人履歷網站

這是我使用 [Hugo](https://gohugo.io/) 靜態網站生成器，搭配 [PaperMod](https://github.com/adityatelange/hugo-PaperMod) 主題打造的個人履歷與作品展示網站。

---

## 主要技術與架構

- 靜態網站生成器：Hugo (v0.147.9)
- 主題：PaperMod，進行部分客製化調整
- 內容管理：
  - 文章分類管理透過 `mainSections` 參數，控制首頁顯示文章類型
  - 支援多語系（i18n）
  - 使用自訂模板強化中文排版與樣式

---

## 特色與設計理念

- **清晰簡潔**：以最少圖片，強調內容本身的價值
- **履歷與成長記錄結合**：不只展示靜態履歷，也記錄學習與專案過程
- **易維護**：分類與標籤系統搭配 Hugo 自動生成列表頁面
- **客製化排版**：改善中文字體排版問題，確保閱讀體驗

---

## 如何運行

1. 確認安裝 Hugo
2. 本地開發：  
3. `hugo server -D`部署：將 `public` 目錄內容部署至 GitHub Pages 或其他靜態網站服務

---

## 其他說明

- `mainSections` 參數在 `config.toml` 中設定，控制首頁展示哪些內容類型  
- 文章中可用 `Params.hiddenInHomeList=true` 隱藏不想在首頁顯示的文章  
- 設計以方便未來擴充與多元內容呈現為目標

---

歡迎參考我的網站 [Note for Clarity](https://minalogs.github.io/) ，若有建議也歡迎提出！

---

*最後更新：2025 年 7 月*

