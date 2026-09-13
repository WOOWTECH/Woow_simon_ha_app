# Simon iBMS — 商店上架文案草稿

**狀態：已定稿並套用至 `fastlane/metadata/`（2026-09-13）。**
本資料夾保留為來源文件與可選段落的存放處。

架構沿用 Home Assistant 原文案的段落骨架（開頭鉤子 → 核心價值 → 功能分點 → 相容性 → 需求說明），
內容全部改寫為 Simon iBMS，不照抄 HA 的事實性宣稱。

## 本草稿的三個處理原則

1. **不挪用 HA 的事實宣稱**
   原文的「over 1 million users」、「Home Assistant Green / Raspberry Pi」、
   100+ 品牌相容清單，都是 Home Assistant 的成績與產品，Simon 不能直接沿用。

2. **未聯調的功能獨立標示**
   推播通知、Apple Watch、Wear OS、Android Auto、CarPlay 的程式碼都在，
   但本次**沒有做聯調或實測**。寫進商店文案等於對使用者承諾，
   下方標記為「⚠️ 可選段落」，請逐項決定保留或刪除。

3. **保留開源歸屬**
   本 App 基於 Home Assistant（Apache 2.0）。文案中保留一行歸屬說明，
   既是授權義務，也避免審核方認為刻意隱瞞來源。

## 需要你或客戶提供的欄位

| 欄位 | 現值 | 需要什麼 |
|---|---|---|
| `copyright.txt` | `2016-2019 Robert Trencheny`（上游作者） | 客戶的法律主體全名與年份，例如 `2026 Simon Electric (Asia Pacific) Ltd.` |
| `support_url` | `https://community.home-assistant.io/c/ios` | 客戶的支援頁；若無專頁可暫用官網 |
| `privacy_url` | 已改為官網首頁 | **真正的隱私政策頁**（首頁不算，見交付文件 §6-1） |
| `marketing_url` | `https://companion.home-assistant.io` | 建議 `https://www.simon-apac.com/` |
| 語系範圍 | iOS 13 個語系 | Simon APAC 是否只需 en / zh-Hant / zh-Hans？其餘 10 個語系要翻譯還是移除？ |

## 檔案

- `ios-en-US.md`、`ios-zh-Hant.md`、`ios-zh-Hans.md` — iOS 各欄位
- `android-en-US.md`、`android-zh-TW.md` — Android short/full description
- `shared-fields.md` — 分類、版權、URL 等共用欄位
