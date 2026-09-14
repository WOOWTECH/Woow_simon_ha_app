# ⚠️ 這個資料夾裡的商店圖片必須全部替換，不可送審

`icon.png` 已替換為 Simon iBMS。**以下項目仍是 Home Assistant 的官方行銷素材：**

| 檔案 | 問題 |
|---|---|
| `featureGraphic.png` | Home Assistant 品牌主視覺 |
| `phoneScreenshots/1-5_en-US.png` | 圖 1 上面直接印著 **"Companion app for your Home Assistant installation"**；圖 5 印著 **"Home Assistant"**；全部使用 HA 的品牌藍與版面設計 |
| `sevenInchScreenshots/`、`tenInchScreenshots/` | 同上 |
| `tvScreenshots/`、`wearScreenshots/` | 同上，且本次未驗收 TV / Wear |

## 為什麼一定要換

1. **品牌不實**：以 Simon iBMS 名義上架，商店頁卻印著 Home Assistant，使用者與審核方都會認定為誤導。
2. **非 Apache 2.0 涵蓋範圍**：Apache 2.0 授權的是**程式碼**，不包含 Home Assistant 的商標、logo 與行銷設計。使用這些圖片可能涉及商標與著作權問題。

## 替換方式

由客戶以自己的示範環境重新截圖並製作行銷圖。

**不要使用開發期間的測試截圖**——本次驗證用的是 開發方內部測試環境，
畫面含真實住家/辦公室的裝置名稱與使用者帳號，屬私人測試資料，不得放進客戶商店素材。

Play 商店圖片規格：
- `featureGraphic.png`：1024 × 500
- 手機截圖：2–8 張，最短邊 ≥ 320px、最長邊 ≤ 3840px
- `icon.png`：512 × 512（已完成）
