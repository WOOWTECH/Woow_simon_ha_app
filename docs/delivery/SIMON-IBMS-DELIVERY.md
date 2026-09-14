# Simon iBMS — 客製交付說明（iOS ＋ Android）

本文件說明本次「Simon iBMS」品牌客製的變更範圍、客戶必須自行提供的配置，以及已知限制。
**本文件不宣稱任何未實測的項目已通過。** 每一項狀態都標明證據來源。

## 1. 基線

| 平台 | Repository | 基線 commit | 客製分支 |
|---|---|---|---|
| iOS | `WOOWTECH/Woow_simon_ha_ios` | `9cda9a120182fe21d5a080ad1be391d9524ca6d2` | `brand/simon-ibms` |
| Android | `WOOWTECH/Woow_simon_ha_app` | `804d937905a71da14d54ae9866c4994d20d320f7` | `brand/simon-ibms` |

兩條基線在客製前皆為 clean working tree。iOS 基線已於 2026-08-15 完成 Phase 4 模擬器驗證
（`docs/verification/phase4-report.md`），並以 build 3001 上過 TestFlight 內部測試。

## 2. 本次變更範圍（僅三項）

### 2.1 App 名稱 → `Simon iBMS`

中文與英文皆顯示 `Simon iBMS`，保留大小寫與空格。

- **iOS**：`PRODUCT_NAME`（App / App Δ / Launcher）、`BRAND_APP_NAME`、
  各 target 的 `CFBundleDisplayName`、以及 139 個本地化檔共 3,934 條文案。
  `CFBundleDisplayName` 與 `CFBundleName` 皆綁 `$(PRODUCT_NAME)`。
- **Android**：`common/src/main/res/values/strings.xml` 與 `values-zh-rTW/strings.xml` 的 `app_name`。

**刻意保留**：`Home Assistant Cloud`（Nabu Casa 的第三方服務名稱，改名即為不實陳述），
共 2 條，由 `replace_strings.py` 白名單保護。

### 2.2 品牌圖片

主檔為客戶提供的向量原圖（方形滿版，無預烘焙圓角），保留 `iBMS` 與 `simon` 全部字樣。

- **iOS**：79 組 iconset／141 個檔。AppIcon 方形滿版不透明（App Store 1024 無 alpha）；
  品牌 logo imageset 保留透明底。
  **第三方標誌 `improv-logo`／`thread`／`ha-cloud-logo` 未更動。**
- **Android**：5 密度的 launcher／round／adaptive foreground／啟動畫面／Play 商店圖。
  adaptive icon 前景縮至 **66dp/108dp 安全區**（`--logo-scale 0.611`），
  背景色 `#ECF1F4`（取自 logo 自身淺色帶）。
  已驗證 circle／squircle／square 三種遮罩下 `iBMS` 與 `simon` 均完整。
- **未更動**：功能性圖示、通知小圖示（通用 MDI 房屋字符，非品牌標誌）、
  使用者照片、設備圖片、HA 伺服器內的任何內容。

### 2.3 品牌外連 → `https://www.simon-apac.com/`

官網、文件、說明、支援、社群、關於等**可點擊的品牌外連**統一指向客戶官網。

**核心功能網址全部保留**，未使用全域替換。詳見 §3 矩陣。

### 2.4 品牌網域變更：`aiot.simon.io` → `www.simon-apac.com`

**原因**：`aiot.simon.io` 經權威 DNS（8.8.8.8）查證為 **NXDOMAIN — 網域不存在**
（`simon.io` 本身存在，僅 `aiot` 子網域不存在）。此為前一輪換裝即存在的缺陷。
依客戶指示，兩平台共 193 處全部改為 `www.simon-apac.com`（已驗證解析正常、HTTPS 200）。

改動後分為兩種性質，客戶須分別處理：

**A. 品牌網域類 — 只差客戶放檔案即可運作**

Universal Links（iOS）／App Links（Android）、NFC 標籤網址、邀請連結。
客戶須在官網根目錄提供兩個關聯檔，否則從網頁連結開啟 App 仍不會運作：

| 平台 | 路徑 | 內容要點 |
|---|---|---|
| Android | `https://www.simon-apac.com/.well-known/assetlinks.json` | `package_name: com.simon.home` ＋ **Play App Signing 金鑰**的 SHA-256 指紋（不是 upload key） |
| iOS | `https://www.simon-apac.com/.well-known/apple-app-site-association` | `appID: <TeamID>.com.simon.home`；須以 `application/json` 回應、不可轉址 |

**B. 服務端點類 — 需要真正的服務主機，官網無法承擔**

以下三個端點現在指向官網，但官網不會有這些 API，會回 404：

| 端點 | 用途 |
|---|---|
| `https://www.simon-apac.com/api/sendPush/android/v1` | Android 推播中繼 |
| `https://www.simon-apac.com/api/checkRateLimits` | 推播額度檢查 |
| `https://www.simon-apac.com/mobile.json` | iOS 伺服器警示資料來源 |

**這三項列為客戶待提供項（見 §4）。本次未做推播聯調，不宣稱可用。**

## 3. URL 影響矩陣

### 已改為 `https://www.simon-apac.com/`

| 平台 | 位置 | 原用途 |
|---|---|---|
| iOS | `AppConstants.WebURLs`（22 個常數） | 官網／入門／文件／疑難排解／beta／評價／翻譯／論壇／社群／repo／issues／NFC 說明等 |
| iOS | `ExternalLink.swift`（4 個） | 文件、回報問題、搜尋 issue、Widget 說明 |
| iOS | 8 個 View 內嵌 `Link`／`openURL` | 通知說明、可操作通知、通知音效、Apple Watch 複雜功能、Settings 官網入口 |
| Android | 約 80 個 `/docs/**` | 感測器／通知／Wear OS／整合／疑難排解說明 |
| Android | `preferences.xml` | 「文件」「檢視完整更新日誌」 |
| Android | `SettingsFragment.kt` | 更新日誌、隱私政策 |
| Android | `WebViewActivity.kt` | 安全性公告 |
| Android | `strings.xml` ×2 | `privacy_url` |
| Android | `ConnectionErrorScreen.kt` | 連線錯誤頁的支援入口 |

### 刻意保留（核心功能，未更動）

| 平台 | 位置 | 用途 |
|---|---|---|
| iOS | `Configuration/Brand.xcconfig` `BRAND_HOST` | 深連結／Universal Links 基底（值已改為 `www.simon-apac.com`，見 §2.4） |
| iOS | 4 份 entitlements 的 `applinks:` | Universal Links（已改為 `www.simon-apac.com`，並移除原本重複列 3 次的條目） |
| iOS | `ServerAlerter.swift` `/mobile.json` | 伺服器警示資料來源（**非**可點擊連結；端點需客戶提供，見 §2.4-B） |
| iOS | `AppConstants.Firebase.pushURLString` | 推播中繼 API |
| iOS | `AppConstants.WebRTC.iceServers` | STUN 伺服器 |
| Android | `gradle.properties` | `sendPush/android/v1`、`checkRateLimits`（端點需客戶提供，見 §2.4-B） |
| Android | `AndroidManifest.xml` ×2（app／automotive） | App Links host（邀請、NFC tag） |
| Android | `LinkHandler.kt` `MY_BASE_DOMAIN` | 通用連結解析協定 |
| Android | `ServerSettingsFragment.kt` `BASE_INVITE_URL` | 邀請連結 |
| Android | `NfcSetupActivity.kt`／`TagReaderActivity.kt` | NFC tag URL |
| 兩平台 | OAuth client ID `woowtech.github.io/Woow_simon_ha_app/android` | 登入契約 |
| 兩平台 | URL scheme `simonhome://`（含 `auth-callback`） | OAuth 回呼／深連結 |
| 兩平台 | `LICENSE`、第三方致謝 | 法律歸屬 |

### 3.1 本次一併修正的既有品牌缺陷

這些是前一輪換裝遺留、在本次實機驗證中發現的問題，不在原始三項需求內但直接影響交付品質：

| # | 問題 | 處置 |
|---|---|---|
| 1 | **開源致謝被誤替換**：`open_source_credits` 基線值為 `Based on Simon SmartHome App (Apache 2.0 License)` — 上游「Home Assistant」被當成品牌殘留一併替換，變成自我指涉的假歸屬 | 回復為 `Based on Home Assistant Companion App (Apache 2.0 License)` ／ `基於 Home Assistant Companion App（Apache 2.0 授權）` |
| 2 | **歡迎頁標題未被替換**：`welcome_home_assistant_title` 值為 `Simon\nSmartHome`（字面 `\n` 跳脫字元），任何以空格比對的替換都會漏掉。原始碼掃描與 APK 字串池皆無法發現，**僅在模擬器實測畫面上現形** | 改為 `Simon iBMS`（中英各一處） |
| 3 | **iOS extension 產物名**：`Configuration/HomeAssistant.xcconfig` 的 `PRODUCT_NAME = SimonSmartHome-$(TARGET_NAME)`（無空格變體），導致 7 個 `.appex` 全名為 `SimonSmartHome-Extensions-*` | 改為 `SimoniBMS-Extensions-*` |
| 4 | **商店上架文案品牌殘留**：iOS 13 個語系的 `name.txt` 與 Android `title.txt` 全為 `Home Assistant` / `Home Assistant Companion` | 全數改為 `Simon iBMS`（商店描述文案另見 §6） |
| 5 | **連線錯誤頁掛第三方社群**：兩平台都有指向 Home Assistant 官方 Discord 與 GitHub Issues 的按鈕，且帶第三方 logo | 收斂為單一支援入口指向客戶官網 |
| 6 | iOS 4 份 entitlements 的 `applinks:` 同一網域重複列出 3 次 | 去重 |

### 3.2 刻意未更動的項目

| 項目 | 理由 |
|---|---|
| `Home Assistant Cloud`（Nabu Casa）字串 ×2 | 第三方服務的真實產品名稱，改名即為不實陳述 |
| `docs/verification/phase4-report.md`、`Tools/brand/rebrand-inventory.md`、`docs/plans/2026-08-14-simon-ios-port.md` 內的 `Simon SmartHome` | **歷史稽核紀錄**。Phase 4 報告記載當時畫面實際顯示的文字，改動等同竄改測試證據。已在各檔頭加註「歷史紀錄，內容未修改」並說明現行名稱 |
| `ic_stat_ic_notification{,_blue}.xml` | 通用 MDI 房屋字符，屬功能性圖示而非品牌標誌 |
| `improv-logo`／`thread`／`ha-cloud-logo` imageset | 第三方標誌 |
| `CrashReporter.swift` 的 `io.robbie.` 判斷 | 上游刻意設計：非上游 bundle ID 時停用當機回報。**代表本 App 不送任何當機資料給 Home Assistant**，為有利的隱私事實 |
| `GenericSounds.csv` 的 `darwinsden.com/.../smarthome-mp3-audio-clips/` | 第三方音效檔的真實外部網址 |
| 原始碼中引用上游 GitHub issue 的註解 | 工程出處，非使用者可見連結 |
| HA WebView 內的所有內容（側邊欄標題、⋮ 選單、授權頁、主題） | 伺服器端內容，非原生品牌層 |

## 3.3 驗證結果

### 模擬器實測（對齊 iOS Phase 4 的 5 項）

環境：iPhone 17 Pro Max / iOS 26.5 Simulator；Android Pixel AVD `woow_store_phone`。
測試伺服器：開發方內部測試環境（Home Assistant Core）。

| # | 項目 | iOS | Android |
|---|---|---|---|
| 1 | Onboarding 品牌畫面 | ✅ iBMS 圖示、「Simon iBMS App」、中文內文正確 | ✅ 標題「Simon iBMS」、`Simon iBMS branding icon` |
| 2 | 連線 ＋ OAuth ＋ 登入回 App | ✅ `simonhome://auth-callback` 成功回 App | ✅ 同樣成功回 App |
| 3 | Dashboard WebView ＋ WebSocket | ✅ 真實實體即時載入 | ✅ 同左 |
| 4 | 深連結 `simonhome://navigate/lovelace/0` | ✅ 系統對話框顯示「Simon iBMS Δ」，導向總覽 | ✅ 導向總覽 |
| 5 | 原生版位無舊品牌殘留 | ✅ 裝置命名／位置／Wi-Fi／系統通知對話框皆正確 | ✅ 裝置命名／位置／通知頁皆正確 |

### 產物層級品牌殘留掃描

| 檢查項 | Android APK | iOS .app |
|---|---|---|
| `Simon SmartHome` 全變體（含 `\n`、無空格、大小寫） | **0** | **0** |
| `Simon iBMS` | 185 | 3,449 |
| `aiot.simon.io`（全可執行檔＋資源） | **0** | **0** |
| `simon-apac.com` | ✅ | ✅ |
| 桌面名稱（全語系唯一值） | `Simon iBMS` | `Simon iBMS Δ`（Debug；Release 為 `Simon iBMS`） |
| Extension 產物名 | — | 7 個皆 `SimoniBMS-Extensions-*` |
| App Links host 保留 | ✅ manifest 內 `www.simon-apac.com` | ✅ entitlements `applinks:` |

### 單元測試：與原始基線逐項對照

為避免把基線既有缺陷算成本次迴歸，另建 worktree 於基線 `804d9379` 跑同一組測試對照。

| | 基線 | 本次分支 |
|---|---|---|
| 總失敗數 | **9** | **7** |

- **本次改動造成的新失敗：0 項**
- **本次改動修好：3 項** — `UrlUtilTest`、`NameYourDeviceViewModelTest`（兩者皆因 `aiot.simon.io` NXDOMAIN 而失敗，換網域後轉綠）、`NameYourDeviceNavigationTest`（原硬寫英文文案，改為讀字串資源）
- 其餘 7 項為基線既有，且在無其他負載下單獨重跑仍然失敗（非偶發不穩定）：
  - 6 項 Compose UI 測試（`ComposeTimeoutException`，等待 `'http://ha.local'`，1500 ms 逾時，與品牌字串無關）
  - 1 項 `IgnoreViolationRulesTest` — 基線最後一個 commit `804d9379`「broaden MIUI font ignore rule 到整個 `miui.util.*`」打破了它自己的迴歸守衛

### 未執行的測試

遠端推播（FCM／APNs）、Apple Watch、Wear OS、Android Automotive、實體裝置操作、
正式 Universal Links／App Links 驗證、16KB page size 實機 OS 測試、Release 簽章產物。

## 4. 客戶必須自行提供或完成的配置

這些項目**開發方無法代為完成，且交付包中刻意不包含**。

| # | 項目 | 平台 | 影響 |
|---|---|---|---|
| 1 | **Apple Team ID** | iOS | 建立 `Configuration/HomeAssistant.overrides.xcconfig`，內容一行 `DEVELOPMENT_TEAM = <客戶 Team ID>`。此檔被 `.gitignore` 排除。**未提供則無法簽章。** |
| 2 | **Apple Bundle IDs ＋ Provisioning Profiles** | iOS | 主體與 7 個嵌入 extension（Intents／Matter／NotificationContent／APNSAttachmentService／PushProvider／Share／Widgets），皆為 `com.simon.home.*`。需在客戶帳號下註冊並產生 profile。 |
| 3 | **Android upload keystore** | Android | 透過環境變數 `KEYSTORE_PATH`／`KEYSTORE_PASSWORD`／`KEYSTORE_ALIAS`／`KEYSTORE_ALIAS_PASSWORD` 提供。 |
| 4 | **`VERSION_CODE`** | Android | 環境變數，預設 1。客戶須依商店現有版本指定，否則無法上傳。 |
| 5 | **`google-services.json`** | Android | **目前缺此檔則完全無法建置任何 flavor**（Google Services plugin 為 app 層無條件套用）。需客戶自建 Firebase 專案，含 `com.simon.home`／`.debug`／`.minimal`／`.minimal.debug` 四個 client。 |
| 6 | **`GoogleService-Info.plist`** | iOS | 目前 repo 內的兩個檔是**上游 Home Assistant 的 Firebase 專案**（`PROJECT_ID = home-assistant-mobile-apps`，`BUNDLE_ID = io.robbie.HomeAssistant`），與 `com.simon.home` 不符，FCM 實際不會運作。需客戶以自己的 Firebase 專案取代。 |
| 7 | **APNs 金鑰** | iOS | 遠端推播所需。本次未做推播聯調。 |
| 8 | **隱私政策與服務條款頁** | 兩平台 | 見 §6 已知限制第 1 項。 |
| 9 | **`assetlinks.json` ／ `apple-app-site-association`** | 兩平台 | 放在 `https://www.simon-apac.com/.well-known/` 下。**未提供則從網頁連結開啟 App 永遠不會運作**（NFC 標籤、邀請連結同）。格式見 §2.4-A。 |
| 10 | **推播中繼／額度／伺服器警示端點** | 兩平台 | 目前指向官網但官網無此 API，會回 404。見 §2.4-B。 |
| 11 | **`copyright.txt` 法律主體確認** | iOS | 已設為 `2026 Simon Electric (China) Co., LTD.`（取自 simon-apac.com 頁尾）。**客戶須確認此主體與其 Apple Developer 帳號登記主體一致**；若帳號在別的 APAC 子公司名下，應以帳號主體為準。 |
| 12 | **商店截圖與 featureGraphic** | 兩平台 | Play 現有截圖是 **Home Assistant 的官方行銷素材**，圖上直接印著「Companion app for your Home Assistant installation」。Apache 2.0 授權的是程式碼，**不含 HA 的商標、logo 與行銷設計**。iOS 則尚無截圖。須由客戶以自己的示範環境重製。詳見 §6-11。 |
| 13 | ~~10 個未翻譯語系~~ | iOS | **已完成。** 13 個語系全部翻譯為 Simon iBMS 文案，字數皆在上限內，無 HA 舊行銷文案殘留。 |
| 14 | **支援頁網址** | 兩平台 | `support_url` 目前暫指官網首頁。 |

## 5. 推播（Push）處置說明

本次**不做推播聯調**，但**未刪除任何推播程式、extension 或配置接口**。

iOS 專案原生具備 team-gated 能力開關機制（`Configuration/HomeAssistant.xcconfig` ＋
`Configuration/Entitlements/activate_special_entitlements.sh`）：

```
ENABLE_PUSH_PROVIDER[sdk=iphoneos*] = $(ENABLE_PUSH_PROVIDER_$(DEVELOPMENT_TEAM))
```

僅上游 Home Assistant 的 Team 有對應值。使用客戶自己的 Team 時，
`com.apple.developer.networking.networkextension`（Local Push）、Critical Alerts、
Thread、CarPlay、Device Name 等特殊 entitlement 會在建置期**自動略過**，
PushProvider extension 仍正常嵌入並簽章。

**結論：客戶不需要申請 Local Push entitlement 也能完成正式簽章與上架。**
若日後要啟用 Local Push，需向 Apple 另行申請並在 xcconfig 增設對應的 `ENABLE_PUSH_PROVIDER_<TEAM>` 變數。

`aps-environment` 在 release entitlements 中目前為 `development`，
客戶若要使用正式 APNs 環境需改為 `production`。

## 6. 已知限制

1. **隱私政策與服務條款指向官網首頁。**
   依客戶決定，`privacy_url` 與設定頁的隱私入口目前指向 `https://www.simon-apac.com/`。
   首頁不是政策頁。**Google Play 資料安全問卷與 App Store 審核通常要求可辨識的隱私政策頁面，
   此項有被退件的風險。** 建議客戶提供正式政策頁網址後，修改以下兩處即可：
   - `common/src/main/res/values/strings.xml` 與 `values-zh-rTW/strings.xml` 的 `privacy_url`
   - `app/src/main/kotlin/.../settings/SettingsFragment.kt` 的 `privacy` preference
   iOS 端目前無獨立政策頁入口。

2. **說明／文件深連結已收斂至官網首頁。**
   原本約 80 個 `/docs/**` 深連結指向不存在的路徑，現統一指向官網首頁。
   按鈕文案仍為「文件」「說明」等。若客戶日後建立說明中心，可逐一改回對應頁面。

3. **連線錯誤頁的支援入口已由 4 顆收斂為 1 顆。**
   原本的「論壇」「GitHub Issues」「Discord」三顆按鈕掛著第三方 logo 且指向
   Home Assistant 自己的社群，屬品牌外洩，已移除。保留單一支援入口指向客戶官網。
   此為本次唯一的 UI 結構變更，可還原。

4. **Android 無法在缺少 `google-services.json` 時建置。** 見 §4 第 5 項。

5. **未測範圍**：遠端推播（FCM／APNs）、Apple Watch、Wear OS、Android Automotive、
   實體裝置操作、正式 Universal Links／App Links 驗證、16KB page size 的實機 OS 測試。
   這些項目本次未執行，交付不宣稱通過。

6. **`QUERY_ALL_PACKAGES` 等敏感權限沿用基線宣告。**
   客戶送審時需自行說明用途與範圍。

7. **商店描述文案仍為 Home Assistant 原文。** 見 §4 第 11 項。交付包含這些檔案是為了保留
   結構與語系目錄，**不代表可直接送審**。

8. **`www.simon-apac.com` 目前未提供 App 關聯檔。** 見 §4 第 9 項。
   Universal Links／App Links 在客戶完成設定前不會運作。

9. **`fastlane/Deliverfile`、`fastlane/lanes/testing.rb` 仍含上游 `io.robbie.HomeAssistant`。**
   本次未更動（客戶自行上架，不會使用我方的 fastlane 發布流程）。若客戶要沿用 fastlane，
   需自行改為 `com.simon.home`。

11. **商店截圖含 Home Assistant 商標素材，絕對不可送審。**
    Play 的 `phoneScreenshots/`、`featureGraphic.png`、`sevenInch`/`tenInch`/`tv`/`wearScreenshots`
    全部是 Home Assistant 的官方行銷圖，圖面上有 Home Assistant 字樣與品牌配色。
    已在 `fastlane/metadata/android/en-US/images/README-MUST-REPLACE.md` 標示。
    **重製時不可使用本次開發驗證的截圖** —— 那些畫面來自 開發方內部測試環境，
    含真實住家／辦公室的裝置名稱與使用者帳號，屬私人測試資料。

12. **商店文案已改為 Simon iBMS，iOS 13 個語系全部完成。**
    `de-DE` `en-US` `es-ES` `es-MX` `fi` `fr-FR` `it` `nl-NL` `no` `ru` `sv` `zh-Hans` `zh-Hant`；
    Android 為 `en-US` ＋ 新建的 `zh-TW`。
    文案刻意**不沿用** Home Assistant 的事實性宣稱（用戶數、自家硬體、百餘品牌清單），
    改以本 App 真實具備的開放協定支援描述。推播、Watch、Wear OS、Android Auto、CarPlay
    等**未聯調**功能未寫入文案（依「能給客戶上架為主」的決定；寫入等於對使用者承諾，
    反而增加退件風險），草稿中另列為可選段落，待功能驗收後才可加入。
    每個語系的 description 保留一處「基於 Home Assistant，Apache 2.0」開源歸屬，
    屬授權義務，不應移除。

10. **基線既有的 7 項單元測試失敗未修復。** 見 §3.3。這些與本次品牌改版無關，
    修復它們會超出本次範圍並動到非品牌程式碼。已提供與基線的逐項對照作為佐證。

## 7. 技術事實清單（供客戶填寫商店問卷）

### 識別碼
- iOS bundle ID：`com.simon.home`（Debug `com.simon.home.dev`）＋ 7 個 extension 子 ID
- Android applicationId：`com.simon.home`（full flavor，無 suffix）／`com.simon.home.minimal`（minimal flavor）／Debug 加 `.debug`
- URL scheme：`simonhome://`
- 版本：iOS `2026.7.3` build `3002`；Android versionName 由建置期決定，versionCode 由 `VERSION_CODE` 指定

### Android 權限
`ACCESS_BACKGROUND_LOCATION` `ACCESS_COARSE_LOCATION` `ACCESS_FINE_LOCATION` `ACCESS_NETWORK_STATE`
`ACCESS_NOTIFICATION_POLICY` `ACCESS_WIFI_STATE` `ACTIVITY_RECOGNITION` `BLUETOOTH` `BLUETOOTH_ADMIN`
`BLUETOOTH_ADVERTISE` `BLUETOOTH_CONNECT` `BLUETOOTH_SCAN` `CALL_PHONE` `CAMERA`
`CHANGE_WIFI_MULTICAST_STATE` `FOREGROUND_SERVICE` `FOREGROUND_SERVICE_DATA_SYNC`
`FOREGROUND_SERVICE_LOCATION` `FOREGROUND_SERVICE_REMOTE_MESSAGING` `INTERNET` `MODIFY_AUDIO_SETTINGS`
`NFC` `PACKAGE_USAGE_STATS` `POST_NOTIFICATIONS` `QUERY_ALL_PACKAGES` `RECEIVE_BOOT_COMPLETED`
`RECORD_AUDIO` `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` `SCHEDULE_EXACT_ALARM` `SYSTEM_ALERT_WINDOW`
`VIBRATE` `WAKE_LOCK` `WRITE_EXTERNAL_STORAGE` `WRITE_SETTINGS`
＋ Health Connect 讀取權限群（`android.permission.health.*`，full flavor）

### iOS 權限用途說明
`NSCameraUsageDescription` `NSCrossWebsiteTrackingUsageDescription` `NSFocusStatusUsageDescription`
`NSLocalNetworkUsageDescription` `NSLocationAlwaysAndWhenInUseUsageDescription`
`NSLocationAlwaysUsageDescription` `NSLocationUsageDescription` `NSLocationWhenInUseUsageDescription`
`NSMicrophoneUsageDescription` `NSMotionUsageDescription` `NSPhotoLibraryAddUsageDescription`
`NSPhotoLibraryUsageDescription` `NSSiriUsageDescription` `NSSpeechRecognitionUsageDescription`

### 主要第三方 SDK
- **Android**：Firebase BOM 34.8.0（Messaging／Crashlytics）、Sentry 8.31.0、
  Play Services（location 21.3.0／wearable 19.0.0／threadnetwork 16.3.0／home 16.0.0）、
  OkHttp 5.3.2、Retrofit 3.0.0、Room 2.8.4、Hilt 2.58、Compose BOM 2026.01.00、
  Health Connect client
- **iOS**：HAKit 0.4.16、Starscream 4.0.9、RealmSwift、PromiseKit 8.1.1、ObjectMapper、
  Sodium、Communicator、MBProgressHUD、ReachabilitySwift、XCGLogger、CPDAcknowledgements、
  Firebase（Messaging／Crashlytics）

### 資料流
App 直接連線至使用者自行指定的 Home Assistant 伺服器（REST ＋ WebSocket）。
開發方不經手、不儲存使用者的家庭資料。推播經 §4 所列的推播中繼端點。

## 8. 建置方式

### iOS
```
# 1. 建立 Team 覆寫檔
echo 'DEVELOPMENT_TEAM = <客戶 Team ID>' > Configuration/HomeAssistant.overrides.xcconfig
# 2. 安裝依賴
bundle install && bundle exec pod install
# 3. 一律使用 workspace，不要開 .xcodeproj
xcodebuild -workspace HomeAssistant.xcworkspace -scheme App -configuration Release archive
```
工具鏈：Xcode 26.6（Build 17F113）／Swift 6.3.3。

### Android
```
export JAVA_HOME=<JDK 21>
export KEYSTORE_PATH=... KEYSTORE_PASSWORD=... KEYSTORE_ALIAS=... KEYSTORE_ALIAS_PASSWORD=...
export VERSION_CODE=<商店下一版版本碼>
# 放入客戶的 app/google-services.json
./gradlew :app:bundleFullRelease   # → AAB（Play 上傳）
./gradlew :app:assembleFullRelease # → APK（側載驗收）
```
工具鏈：JDK 21、Gradle 9.2.1、AGP／Kotlin 2.3.0。

## 9. 交付包不包含

私鑰、密碼、token、簽章憑證、Firebase 客戶端設定檔、私人測試資料、
其他客戶或 WOOW 的帳號素材、Debug／Simulator 產物冒充的正式包。
