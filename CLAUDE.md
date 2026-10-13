# 雪語（snow-talk）專案背景

給接手這個 repo 的 Claude 看的交接說明。細節見 `README.md`。

## 目標

滑雪時團隊成員戴 AirPods，手機之間點對點連線（不需要行動網路）互相通話，體驗接近 Cardo 的 mesh 對講機，但只用手機和 AirPods。

## 已定案的決定

- 走「手機對手機點對點」，不走網路語音（雪場收訊差）。
- AirPods 只當手機的麥克風和喇叭，人與人的連線全在手機之間。
- UI 要幾乎不用碰螢幕（戴手套、手機在口袋），用耳機提示音當主要回饋。暗色、高對比、大按鈕。
- 現有同類 App（Murmur 等）的問題：距離短（單跳約 20–50 m）、UI 難看、AirPods 音質。這是自己做的理由。

## 目前進度

- 已完成：`prototype/index.html`，可點的 UI 原型，只有畫面和模擬資料，沒有任何真實通訊。
- 尚未開始：原生 App、通訊層。

## 還沒決定

- 朋友是否全部用 iPhone？這決定傳輸層：全 iPhone 用 Multipeer Connectivity；混用 Android 要走 Wi-Fi Aware 或 BLE L2CAP。
- 使用者有沒有 Mac？沒有 Mac 就不能做原生 iOS（Xcode 只有 macOS），要改跨平台方案。

## 建議的下一步

1. 先驗證最大風險：兩支 iPhone 用 Multipeer 加 AirPods，鎖屏後能否持續通話（iOS 背景限制）。
2. 再做 Opus 編碼、VAD、多人混音，最後做 mesh 轉送和重連。
3. 到雪場實測風噪、距離、低溫電量。

## 注意

- 這個專案和 BiteLogic（`l96178/bitelogic-bot`）無關，不要混在一起。
- 雲端 session 是 Linux，不能編譯或執行 iOS App；Swift 開發要在 Mac 上。
