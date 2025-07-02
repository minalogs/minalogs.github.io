---
date: '2025-07-01T01:00:49+08:00'
draft: false
hidemeta: false
comments: false
tags: ["HUGO", "GitHub"]
cover:
    image: "<image URL>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
title: '換 GitHub 帳號與 Hugo 子模組錯誤：解決兩個部署上的常見問題'
description: "Desc Text."
canonicalURL: 'https://https://minalogs.github.io/post/hugo-blog-deploy'
author: "Mina Liu"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
type: posts
---

# 換 GitHub 帳號與 Hugo 子模組錯誤：解決兩個部署上的常見問題

## 前言：關於這次的任務

這次的目標是將我以 Hugo 架設的個人履歷網站部署到 GitHub Pages，作為後續技術紀錄與作品整理的起點。我使用新的 GitHub 帳號 minalogs，希望讓資料分析與系統設計相關的學習歷程，與過去的開發專案有所區隔。

在部署過程中遇到了兩個技術問題，分別與帳號授權及子模組設定有關。雖然進度比預期慢了一些，但也因此釐清了幾個常見的操作誤區，以下紀錄這次處理方式與思考脈絡。

---

## 問題一：更換 GitHub 帳號後無法 push

我將原本的 GitHub 帳號切換成新的 `minalogs`，準備將網站部署到新帳號的儲存庫中。然而 push 時出現以下錯誤訊息：

```
You don't have permissions to push to 'minalogs/minalogs' on GitHub.
```

### 初步嘗試：調整 Git 使用者資訊

我嘗試使用以下指令更新 `user.name` 與 `user.email`：

```bash
git config user.name "minalogs"
git config user.email "minalogs.work@gmail.com"
```

但這並未解決問題。推測可能與本地端與 GitHub 帳號的授權連結方式有關。

---

### 解法：使用 SSH 金鑰重新連線帳號

後續改採 SSH 金鑰進行連線，問題順利解決。

#### SSH 設定步驟（macOS 範例）：

1. 產生 SSH 金鑰：

   ```bash
   ssh-keygen -t ed25519 -C "minalogs.work@gmail.com"
   ```

2. 複製公開金鑰至剪貼簿：

   ```bash
   pbcopy < ~/.ssh/id_ed25519.pub
   ```

3. 登入 GitHub → 前往 `Settings > SSH and GPG keys` → 新增金鑰

4. 若使用多組帳號，可在 `~/.ssh/config` 中設定不同身份：

   ```bash
   Host github.com-minalogs
     HostName github.com
     User git
     IdentityFile ~/.ssh/id_ed25519_minalogs
   ```

5. 更新專案的 remote：

   ```bash
   git remote set-url origin git@github.com-minalogs:minalogs/my-hugo-site.git
   ```

目前我對 SSH 原理的理解尚不深入，但透過清楚命名與設定，可以有效解決多帳號切換下的授權問題。

---

## 問題二：使用 Hugo 主題時無法推送子模組

我使用 Hugo 建站時，透過以下指令加入主題（[PaperMod](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation)）：

```
git clone https://github.com/adityatelange/hugo-PaperMod themes/PaperMod --depth=1
```

在設定[GitHub Action 的自動部署](https://gohugo.io/host-and-deploy/host-on-github-pages/)時出現錯誤，提示子模組無法推送：

```
build
No url found for submodule path 'themes/PaperMod' in .gitmodules
```

### 問題分析與解法：

子模組會建立獨立的 Git 追蹤紀錄，因此若未妥善管理，會導致 push 失敗。子模組會建立獨立的 Git 追蹤紀錄，因此若未妥善管理，會導致 push 失敗。考量未來可能會修改主題內容，我決定將其直接整合進主專案中，不再保留 submodule。

#### 處理步驟：

```
# 進入專案根目錄
cd /Users/.../noteforclarity

# 1. 從 Git 索引移除（保留資料夾內容）
git rm -r --cached themes/PaperMod

# 2. 移除本地的子模組設定資料夾
rm -rf themes/PaperMod/.git

# 3. 移除父專案的 .gitmodules 檔（如果存在）
rm -f .gitmodules

# 4. 從 config 中刪除子模組紀錄（即使可能不存在也可忽略）
git config --remove-section submodule.themes/PaperMod || true

# 5. 重新加入 themes 資料夾（當作普通 folder）
git add themes/PaperMod

# 6. 提交此次修改記錄
git commit -m "Convert PaperMod to regular folder (remove submodule)"

# 7. 確認主題仍可運作後，將其推送至遠端並部署
git push

```

這樣的方式雖不如原先設計那樣模組化，但對於我目前個人維護的網站而言，是較穩定且容易掌控的選項。

> 📝 補充說明：在小型、個人維運的靜態網站中，相比維持與上游主題同步，可控性與穩定性往往更重要。這是我這次選擇放棄 submodule 的主要原因。
>
> 即使我移除了 submodule 的連動，未來若主題作者有更新，我仍可以選擇性地透過 git clone 或直接下載方式取得新版內容，再視需要整合進我目前的專案。這樣的處理方式，在不頻繁變動的前提下，更利於維護與客製化。

---

## 結語：關於這次的調整與取捨

這次的過程讓我進一步了解 Git 子模組與 SSH 認證的運作邏輯，也重新思考專案維護時的權衡原則。雖然時間花得比預期多，但對於未來部署與結構設計會更有掌握。

這篇記錄僅供自己與有類似需求的使用者參考，若有更精簡或合適的做法，也歡迎交流指教。

