# 日々日本語｜每日一課部落格樣板

藍白色系、手機版型、繁體中文導覽、七天 N5 入門教材、折疊答案、文章前後篇、分類與按月歸檔。使用 Jekyll + GitHub Pages + GitHub Actions；不需要資料庫或 AI API 金鑰。

## 快速上線

1. 在 GitHub 建立一個公開儲存庫（例如 `japanese-blog`），預設分支使用 `main`。
2. 解壓縮後，將本資料夾內的檔案放在儲存庫根目錄；務必包含隱藏的 `.github/workflows/daily-publish.yml`。不要把整個外層資料夾當作網站根目錄上傳。可使用 GitHub 網頁 Add file → Upload files；若無法選到隱藏資料夾，請用 git 推送。
3. 修改 `_config.yml`：

```yaml
url: "https://YOUR-NAME.github.io"
baseurl: "/japanese-blog"
author: "你的名字"
```

若儲存庫是 `YOUR-NAME.github.io`，`baseurl` 請設為 `""`。若使用自訂網域，依實際網站根目錄設定。請保留 `timezone: Asia/Taipei`。

4. 到 Settings → Pages → Build and deployment → Source，選 **GitHub Actions**。
5. Settings → Actions → General：允許此工作流程使用的 Actions。若儲存庫規則禁止 Actions 推送 main，請調整為允許此專用儲存庫的發布機器人寫入，或改用人工發布；請勿直接取消既有重要專案的分支保護。
6. 到 Actions → Daily Japanese Blog → Run workflow → main → Run workflow。
7. 等待 build 與 deploy 都成功；網站網址為 `https://YOUR-NAME.github.io/japanese-blog/`。首次執行會發布第 1 課，另外保留一篇可刪除的示範開場文章。

**注意：只要 push 到 main 也會檢查並發布到期教材，因此請在準備好後才進行第一次執行。**

## 每日排程

預設臺灣時間每日 07:17 啟動：`17 23 * * *`（UTC）。GitHub 排程可能延遲，不保證準時；手動執行可補跑。工作流程必須位於預設分支；此樣板假設分支名稱為 main。公開儲存庫長期沒有活動可能停用排程，請定期查看 Actions。

首次執行將當天臺灣日期記為開始日，存入 `scripts/state.json` 並提交。後續每天發布一課；漏跑時補齊所有到期課程，文章使用原應發布日期。當天重跑不重複。七課用完後不會憑空產生教材，網站仍正常部署；請持續補充 `lessons/`。

同一個 workflow 完成發布、提交、建置與部署，不依賴機器人的 commit 再觸發另一個 workflow。

## 編輯與增加教材

* 修改 `lessons/001.json` 等檔案，使用 UTF-8。`body` 是 Markdown，換行使用 JSON 的 `\n`。
* 複製最後一個教材，命名 `008.json`、`009.json`……檔名保持相同寬度並依順序排列；超過 999 課時請統一增加寬度。
* 每課需有唯一 `id`、英文小寫 `slug`、`title`、`category`、`level`、`tags`（字串陣列）、`summary` 與 `body`。
* `body` 至少包含 `## 今日目標`、`## 今日練習`，建議另有單字、用法、例句與參考答案。
* 已開始發布後，**只在末尾追加新課，勿插入或重新排序既有教材**，避免順序與原定日期變動。
* 已發布教材要改正，請直接修改 `_posts/` 內相應文章；修改 queue 不會覆蓋已發布文章。
* `scripts/state.json` 是正式發布紀錄，請勿任意刪除。若刪除可能重新起算並重複發布。需要更改開始日，請在理解原發布狀態後修改該檔案。
* JSON 語法錯誤、重複 ID、缺少必要欄位或檔名衝突會使工作流程失敗；到 Actions 查看紅色步驟。修正後手動重跑。
* 此樣板不會自動驗證日文正確性，正式教學前請自行審閱。

## 個人化

* 網站名稱、介紹與作者：`_config.yml`
* 首頁介紹、固定的「今日のことば」（目前不是每日輪替）：`index.html`
* 個人簡介與目標：`about.md`
* 顏色、字型、手機排版：`assets/style.css`
* 網頁與文章版型：`_layouts/`
* 開場示範文章：`_posts/2026-11-07-welcome.md`，可修改日期或刪除。

## 在電腦上預覽（選用）

安裝 Ruby 與 Bundler 後，在此資料夾執行：

```bash
bundle install
bundle exec jekyll serve --baseurl ""
```

瀏覽 `http://127.0.0.1:4000/`。未執行發布程式時只會顯示開場示範文章。若想測試發布，請先複製一份資料夾，於複本執行 `python scripts/publish_daily.py`，避免將測試用 state 提交到正式儲存庫。

## 排程故障檢查

* 無執行紀錄：確認預設分支、workflow 啟用狀態與 UTC 排程，或 Run workflow。
* git push 被拒：檢查儲存庫 Actions 權限、分支規則與其他人在執行期間是否有推送；確認後重跑。
* 網站 CSS 404：檢查 baseurl 是否等於儲存庫名稱並重新部署。
* Pages 部署被拒：檢查 Pages source 與 github-pages environment 的分支部署規則。
* Actions 成功卻沒有新課：檢查當日是否已發布、教材是否用完。

## 驗證範圍

隨附教材發布程式已檢查首次發布、同日重跑、漏跑補齊與教材耗盡行為；教材 JSON 與 workflow YAML 已解析檢查。此產出環境沒有 Ruby/Jekyll，因此未在本機執行 Jekyll 完整建置；請以首次 GitHub Actions build/deploy 成功為上線確認。

## 官方參考

* https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/adding-content-to-your-github-pages-site-using-jekyll
* https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages
* https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows
