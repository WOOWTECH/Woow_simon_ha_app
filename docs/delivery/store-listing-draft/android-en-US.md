# Android（Google Play）— en-US

Play 的欄位限制與 App Store 不同：
- `title.txt` 30 字元
- `short_description.txt` **80 字元**
- `full_description.txt` 4000 字元

## title.txt  (已套用)
```
Simon iBMS
```

## short_description.txt  (80 字元上限，目前 72)
```
Control lighting, climate and scenes across your space. Local-first, private.
```
**已定案（使用者 2026-09-13 選定功能導向版本）。** 備選的品牌導向版本已移除。

## full_description.txt  (4000 字元上限)

```
Simon iBMS puts your entire space in your pocket. Connect to your Simon iBMS server to monitor and control lighting, switches, climate, curtains, scenes and sensors — from the next room or from the other side of the world.

ONE APP FOR THE WHOLE SPACE
Every room, every circuit, every scene appears on a single dashboard that mirrors your installation in real time. Tap a light, drag a dimmer, run a scene — the change happens the moment you make it.

LOCAL FIRST, PRIVATE BY DESIGN
Simon iBMS runs on a server in your own building. Your usage data stays on site. When your phone is on the local network the app talks to the server directly, so control stays fast and keeps working even when the internet does not. Simon iBMS does not collect or store your space's data — everything the app shows comes from your own server.

AUTOMATION THAT FITS THE WAY YOU WORK
Let lighting follow the sunset, climate follow occupancy, and the whole floor shut down when the last person leaves. Build the logic on your server; this app is the remote for it.

YOUR PHONE BECOMES PART OF THE SYSTEM
- Home screen widgets for one-tap control of the devices you use most
- NFC tags — tap your phone on a tag to run a scene
- Optionally share your device's sensors, such as battery level, connectivity and next alarm, so automations can react to them
- Optionally share your location to drive arrival and departure automations. Location is shared only with your own server and is never sent to a third party.
- Quick Settings tiles and device controls for fast access without opening the app

OPEN FOUNDATION, OPEN STANDARDS
Simon iBMS is built on Home Assistant, the open-source home and building automation platform, and is distributed under the Apache 2.0 License. That means an open, inspectable foundation and support for the open standards your installation already speaks — including Zigbee, Z-Wave, Matter, Thread, KNX, Modbus and MQTT.

WHAT YOU NEED
- A configured Simon iBMS server that this app can reach
- An account on that server

For control from outside your building, your server must be reachable from the internet over a valid HTTPS certificate. Inside your own network, no external access is required.
```

---

## ⚠️ 可選段落 — 確認功能已聯調後才加入

### A. 推播通知（需客戶提供 google-services.json 並完成 FCM 聯調）
```
- Get notified about what matters — a door left open, a leak detected, a circuit tripped. You decide what the system tells you and when.
```
**注意**：Android 目前**缺 `google-services.json` 就完全無法建置**，
所以這一段的前提本來就必須先滿足才會有正式包。

### B. Wear OS（需實機驗收）
```
- Wear OS support, including tiles and watch face complications
```

### C. Android Auto（需實機驗收）
```
- Android Auto lets you control your space from your car's dashboard — open the garage, disable the alarm, and more
```

### D. 語音助理（需實機驗收）
```
- Text or talk to the voice assistant running on your own server
```

---

## changelogs/

Play 的版本說明放在 `fastlane/metadata/android/en-US/changelogs/<versionCode>.txt`，
**500 字元上限**。首版建議：

```
First release of Simon iBMS.

Connect to your Simon iBMS server and control lighting, switches, climate, curtains, scenes and sensors across your whole space — locally on your own network, or remotely when you are away.

Includes a real-time dashboard, home screen widgets, NFC tag support, and optional device sensor and location sharing for automations.
```
（目前 336 字元）
