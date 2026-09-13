# iOS — en-US

## name.txt  (30 字元上限)
```
Simon iBMS
```

## subtitle.txt  (30 字元上限，目前 28)
```
Intelligent Building Control
```

## keywords.txt  (100 字元上限，逗號分隔，目前 96)
```
smart home,building,automation,lighting,scene,switch,climate,sensor,control,iBMS,local,IoT
```

## promotional_text.txt  (170 字元上限，可隨時更新不需送審)
```
Control lighting, climate, scenes and sensors across your whole space — from the next room or the other side of the world.
```

## description.txt  (4000 字元上限)

```
Simon iBMS puts your entire space in your pocket.

Connect to your Simon iBMS server to monitor and control lighting, switches, climate, curtains, scenes and sensors — from the next room or from the other side of the world.

ONE APP FOR THE WHOLE SPACE
Every room, every circuit, every scene appears on a single dashboard that mirrors your installation in real time. Tap a light, drag a dimmer, run a scene — the change happens the moment you make it.

LOCAL FIRST
Simon iBMS runs on a server in your own building. Your usage data stays on site. When your phone is on the local network the app talks to the server directly, so control stays fast and keeps working even when the internet does not.

AUTOMATION THAT FITS THE WAY YOU WORK
Let lighting follow the sunset, climate follow occupancy, and the whole floor shut down when the last person leaves. Build the logic on your server; the app gives you a remote for it.

BUILT FOR YOUR PHONE
- Home screen widgets for one-tap control of the devices you use most
- NFC tags — tap your phone on a tag to run a scene
- Optionally share your device's sensors, such as battery level and connectivity, so automations can react to them
- Optionally share your location to drive arrival and departure automations. Location is shared only with your own server and is never sent to a third party.

OPEN FOUNDATION
Simon iBMS is built on Home Assistant, the open-source home and building automation platform, and is distributed under the Apache 2.0 License. That means an open, inspectable foundation and support for open standards such as Zigbee, Z-Wave, Matter, Thread, KNX, Modbus and MQTT — the protocols your installation already speaks.

WHAT YOU NEED
- A configured Simon iBMS server that this app can reach
- An account on that server

For control from outside your building, your server must be reachable from the internet over a valid HTTPS certificate. Inside your own network, no external access is required.

Simon iBMS does not collect or store your home data. Everything the app shows comes from your own server.
```

---

## ⚠️ 可選段落 — 確認功能已聯調後才加入

以下段落的程式碼都在 App 內，但**本次未做聯調或實測**。
每一段只有在對應功能確認可用後才可放進 `description.txt`。

### A. 推播通知（需客戶完成 APNs 金鑰與 Firebase 專案設定）
```
- Get notified about what matters — a door left open, a leak detected, a circuit tripped. You decide what the system tells you and when.
```

### B. Apple Watch（需實機驗收）
```
- Apple Watch app and complications: run scenes and check status from your wrist
```

### C. CarPlay（需 Apple 授權與實機驗收）
```
- CarPlay support: open the garage or disable the alarm from your dashboard
```

### D. Siri 捷徑（需實機驗收）
```
- Siri Shortcuts: run any scene with your voice
```

---

## release_notes.txt  (4000 字元上限)

首版建議寫法：

```
First release of Simon iBMS.

Simon iBMS connects to your Simon iBMS server and gives you control of lighting, switches, climate, curtains, scenes and sensors across your whole space — locally on your own network, or remotely when you are away.

This first version includes:
- Real-time dashboard that mirrors your installation
- Direct local connection when your phone is on the same network
- Secure sign-in to your own server
- Home screen widgets for one-tap control
- NFC tag support for running scenes
- Optional device sensor and location sharing for automations

We would like to hear how it works in your space.
```

**注意**：若採用上方任何「可選段落」的功能，release notes 也要同步列出，
否則使用者會找不到商店描述提到的功能。
