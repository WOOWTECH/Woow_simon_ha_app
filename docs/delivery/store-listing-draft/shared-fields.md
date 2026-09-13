# 共用欄位（非語系相關）

## iOS `fastlane/metadata/`

| 檔案 | 現值 | 建議值 | 說明 |
|---|---|---|---|
| `copyright.txt` | `2016-2019 Robert Trencheny` | **需客戶提供** | 客戶法律主體全名與年份，例如 `2026 Simon Electric (Asia Pacific) Ltd.`。這是上游作者的版權宣告，**必須換掉**。 |
| `primary_category.txt` | `MZGenre.Lifestyle` | 維持 | 生活風格，合適 |
| `secondary_category.txt` | `MZGenre.Productivity` | 可考慮 `MZGenre.Utilities` | 工具類比生產力更貼近 iBMS 控制 App |

## 每個語系都要改的 URL 欄位

| 檔案 | 現值 | 建議值 |
|---|---|---|
| `marketing_url.txt` | `https://companion.home-assistant.io` | `https://www.simon-apac.com/` |
| `support_url.txt` | `https://community.home-assistant.io/c/ios` | **需客戶提供支援頁**；無專頁則暫用 `https://www.simon-apac.com/` |
| `privacy_url.txt` | 已改為 `https://www.simon-apac.com/` | **需客戶提供真正的隱私政策頁**（首頁不算，見交付文件 §6-1） |
| `apple_tv_privacy_policy.txt` | — | 本 App 無 tvOS 版本，留空即可 |

## Play 資料安全問卷會問到的事實（供客戶填寫）

這些是本次從原始碼確認的技術事實，不是行銷文案：

| 項目 | 事實 |
|---|---|
| 資料傳送對象 | 使用者自行指定的 Simon iBMS 伺服器（REST + WebSocket）。開發方不經手、不儲存。 |
| 位置資料 | 僅在使用者明確同意後分享，且只傳給使用者自己的伺服器。 |
| 當機回報 | **不送出**。`CrashReporter.swift` 限定只有上游 bundle ID 才啟用，本 App 的 `com.simon.home` 不符合，因此停用。 |
| 分析 | 未啟用第三方分析 SDK。 |
| 推播 | 程式碼存在但**本次未聯調**；需客戶自建 Firebase 專案與 APNs 金鑰。 |
| 敏感權限 | `QUERY_ALL_PACKAGES` 等沿用基線宣告，送審時需自行說明用途。 |

## 語系範圍決策

iOS 目前有 13 個語系目錄：
`de-DE` `en-US` `es-ES` `es-MX` `fi` `fr-FR` `it` `nl-NL` `no` `ru` `sv` `zh-Hans` `zh-Hant`

Simon APAC 若主要市場是亞太，建議：
- **保留**：`en-US`、`zh-Hant`、`zh-Hans`
- **移除或翻譯**：其餘 10 個

留著未翻譯的語系目錄會讓那些市場的商店頁顯示 Home Assistant 的舊文案，
**比沒有那個語系更糟**。這需要你或客戶決定。

Android 目前只有 `en-US` 一個語系。
