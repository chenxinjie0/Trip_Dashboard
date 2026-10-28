# Trip Dashboard

一組給旅行用的小工具網頁，每一頁都是一個獨立的 HTML 檔，設計成可以直接嵌入 Notion（`/embed`）使用。不需要建置、不需要後端，也不需要 API 金鑰。

| 檔案 | 名稱 | 用途 |
| --- | --- | --- |
| [`trip.html`](trip.html) | 旅途小面板 | 當地時間＋出發倒數＋今日天氣＋七天預報，整合在一張卡片 |
| [`weather.html`](weather.html) | 一週天氣預報 | 目前天氣與七天預報，每天以共用溫度刻度的高低溫條呈現 |
| [`clock.html`](clock.html) | 當地時間時鐘 | 任一時區的時間、日期與 UTC 偏移 |
| [`index.html`](index.html) | 倒數計時器 | 倒數到指定日期時間，或當作分鐘計時器使用 |

## 共同特色

- **單檔、零依賴**：HTML、CSS、JavaScript 全部寫在同一個檔案，只從 Google Fonts 載入 Noto Sans TC 與 IBM Plex Mono 字型。
- **深色模式**：跟隨系統的 `prefers-color-scheme`，也可以在 `<html>` 加上 `data-theme="light"` 或 `data-theme="dark"` 強制指定。
- **記住設定**：每頁的設定都存在瀏覽器的 `localStorage`，重新開啟時會沿用上次的選擇（存取失敗時會安靜地退回預設值）。
- **網址參數**：大部分頁面可以用網址參數指定初始內容，優先順序為「網址參數 > 上次設定 > 預設值」，方便在 Notion 中為不同行程嵌入不同設定。
- **適合嵌入**：卡片寬度約 520–560px，手機寬度也能正常顯示，並支援瀏海螢幕的 safe area。

## 各頁說明

### 旅途小面板 `trip.html`

一張卡片包含：地點名稱、當地時間與日期、出發倒數天數、今日天氣、七天天氣條。點右上角「設定」可以展開設定區。

**資料來源**

- **日本**：使用[日本氣象廳](https://www.jma.go.jp/)的預報資料，內建 56 個預報區域（從宗谷到八重山）。週間預報缺少的降雨機率與氣溫，會用短期預報（今明兩天）補上。氣象廳資料取得失敗時，會自動改用 Open-Meteo，並在畫面上說明原因。
- **台灣**：內建 22 個縣市，使用 [Open-Meteo](https://open-meteo.com/)。
- **其他地區**：用 Open-Meteo Geocoding 搜尋地名後選擇。

**網址參數**

| 參數 | 說明 | 範例 |
| --- | --- | --- |
| `jp` | 日本氣象廳區域代碼 | `?jp=130000`（東京） |
| `tw` | 台灣縣市名稱（「台」「臺」皆可） | `?tw=臺北市` |
| `city` | 搜尋地名，取第一筆結果 | `?city=Seoul` |
| `lat`、`lon`、`name` | 直接指定座標與顯示名稱 | `?lat=48.85&lon=2.35&name=巴黎` |
| `title` | 倒數標題 | `?title=出發` |
| `date` | 倒數日期（`YYYY-MM-DD`） | `?date=2027-02-13` |

未指定地點時，預設為日本「宮城」（仙台）；預設倒數為「出發」，日期 `2027-02-13`。

### 一週天氣預報 `weather.html`

顯示目前氣溫、體感溫度、濕度、風速，以及七天的天氣、高低溫、降雨機率與降雨量。七天的高低溫條畫在同一個溫度刻度上，方便比較。資料來自 Open-Meteo。

- 可以搜尋地名、使用「目前位置」（瀏覽器定位），或點快捷按鈕（仙台、台北、東京、新潟、會津若松）。
- 網址參數：`city`（搜尋地名），或 `lat`、`lon`、`name`（指定座標）。
- 預設地點：仙台。

### 當地時間時鐘 `clock.html`

顯示指定時區的時間、日期、UTC 偏移，以及與本機時間的差距。

- 時區欄位支援輸入英文搜尋（例如 `Tokyo`），清單來自瀏覽器的 `Intl.supportedValuesOf("timeZone")`。
- 快捷按鈕：台北、東京、首爾、倫敦、紐約、洛杉磯。
- 可以自訂顯示名稱，並切換 24／12 小時制。
- 網址參數：`tz`（IANA 時區，例如 `Asia/Tokyo`）、`label`（顯示名稱）、`h12=1`（12 小時制）或 `h12=0`（24 小時制）。
- 預設時區：`Asia/Tokyo`。

### 倒數計時器 `index.html`

兩種模式，用上方分頁切換：

- **倒數日期**：輸入標題與目標日期時間，顯示剩餘的天、時、分、秒；超過目標後改為顯示已經過了多久。預設為「新年倒數」，目標是下一個元旦。
- **計時器**：設定分鐘與秒數（或點 5／10／25／45／60 分快捷按鈕），可開始、暫停、繼續、重設，並以圓環顯示進度。時間到會發出三聲提示音，計時中分頁標題也會顯示剩餘時間。

## 使用方式

### 本機開啟

直接用瀏覽器開啟 HTML 檔即可。若瀏覽器因 `file://` 限制擋下 API 請求，可以在專案資料夾啟動一個簡單的靜態伺服器：

```bash
python -m http.server 8000
```

然後開啟 `http://localhost:8000/trip.html`。

### 嵌入 Notion

1. 把檔案放到任一靜態網站空間，例如 GitHub Pages（在 repo 的 Settings → Pages 啟用）。
2. 在 Notion 頁面輸入 `/embed`，貼上網址，例如：

   ```text
   https://<帳號>.github.io/Trip_Dashboard/trip.html?jp=040000&title=出發&date=2027-02-13
   ```

3. 調整嵌入區塊的大小即可。

## 外部服務

| 服務 | 用途 | 使用頁面 |
| --- | --- | --- |
| [Open-Meteo Forecast API](https://open-meteo.com/en/docs) | 天氣預報 | `trip.html`、`weather.html` |
| [Open-Meteo Geocoding API](https://open-meteo.com/en/docs/geocoding-api) | 地名搜尋 | `trip.html`、`weather.html` |
| [日本氣象廳 防災情報 JSON](https://www.jma.go.jp/bosai/forecast/) | 日本天氣預報 | `trip.html` |
| [Google Fonts](https://fonts.google.com/) | 字型 | 全部 |

以上服務皆不需要申請金鑰。
