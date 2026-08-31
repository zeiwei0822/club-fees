# 💰 社費繳費紀錄網頁

打名字就能記錄誰交過社費，資料存在雲端（免費），任何裝置打開都看得到同一份資料。

---

## 一、開一個免費雲端資料庫（Firebase）

1. 到 https://console.firebase.google.com/ ，用 Google 帳號登入。
2. 點 **建立專案（Add project）** → 取個名字（例如 `club-fees`）→ 一直按繼續（Google Analytics 可以關掉）→ 建立。
3. 左邊選單點 **Build → Firestore Database** → 按 **建立資料庫（Create database）**。
   - 位置隨便選離你近的（例如 `asia-east1`）。
   - 模式先選 **以測試模式開始（Start in test mode）** → 完成。
4. 拿「網頁設定」：
   - 點左上角齒輪 ⚙️ → **專案設定（Project settings）**。
   - 往下捲到 **你的應用程式（Your apps）**，點 **`</>`（Web）** 這個圖示。
   - 取個暱稱 → 註冊 → 它會給你一段 `firebaseConfig = { ... }`。
5. 把那段 `firebaseConfig` 的內容，**複製貼到 `index.html` 裡對應的地方**（檔案中有標「👇 換成你自己的設定 👇」）。

### ⚠️ 重要：設定資料庫權限

測試模式預設 30 天後會鎖住。因為這是簡單的社費紀錄，建議改成「大家都能讀寫」：

在 Firestore 的 **規則（Rules）** 分頁，貼上這段後按 **發布（Publish）**：

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /fees/{doc} {
      allow read, write: if true;
    }
  }
}
```

> 注意：這代表「知道網址的人都能新增/刪除紀錄」。給社團內部用沒問題；若要更安全可再加登入機制，需要時再跟我說。

---

## 二、推上 GitHub Pages

1. 到 https://github.com/ 註冊/登入 → 右上角 **+** → **New repository**。
2. Repository name 取例如 `club-fees` → 選 **Public** → **Create repository**。
3. 上傳檔案（最簡單的方式）：
   - 在 repo 頁面點 **uploading an existing file**。
   - 把 `index.html` 和 `README.md` 拖進去 → **Commit changes**。
4. 開啟 Pages：
   - repo 上方 **Settings** → 左邊 **Pages**。
   - **Source** 選 `Deploy from a branch`，Branch 選 `main` / 資料夾 `/ (root)` → **Save**。
5. 等 1～2 分鐘，網址會出現在同一頁，長得像：
   `https://你的帳號.github.io/club-fees/`

打開那個網址就能用了 🎉

---

## 用命令列上傳（進階，選用）

若你想用 git 指令而不是網頁拖拉：

```bash
cd C:/Users/User/Desktop/money
git init
git add .
git commit -m "社費繳費紀錄網頁"
git branch -M main
git remote add origin https://github.com/你的帳號/club-fees.git
git push -u origin main
```

之後一樣到 **Settings → Pages** 開啟即可。

---

## 兩個頁面

| 頁面 | 網址 | 用途 |
|------|------|------|
| 檢視版 | `.../club-fees/` | 分享給大家看，只能看不能改 |
| 管理版 | `.../club-fees/admin.html` | 你自己用，可新增/標記/刪除（網址別公開） |

兩頁看同一份雲端資料、即時同步。

## 功能
- **管理版**：一次匯入整批社員名單、單筆新增、把人標記「已繳/未繳」、刪除
- **檢視版**：清楚看到「還沒繳」與「已繳費」兩張名單、繳費比例、搜尋名字
- 即時同步：多人多裝置看到同一份資料
- 沒有密碼；管理權限靠「不公開 admin.html 網址」來區分
