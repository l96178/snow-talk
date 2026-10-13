# 雪語（ski-intercom）

滑雪時，團隊成員戴 AirPods，透過手機之間的點對點連線互相通話，不需要行動網路。目標是接近 Cardo 那類 mesh 對講機的使用體驗。

> 這個資料夾和 repo 內其他檔案（BiteLogic）無關，是獨立專案。之後可以整個搬到自己的 repo。

## 架構

```
我的 AirPods ⇄(藍牙)⇄ 我的手機 ⇄(點對點無線 / mesh 接力)⇄ 隊友手機 ⇄(藍牙)⇄ 隊友 AirPods
```

AirPods 只是手機的麥克風和喇叭。人與人之間的連線全部發生在手機之間。

## 目前進度

- [x] `prototype/index.html`：可點的 UI 原型（純 HTML，無相依套件）。用瀏覽器開啟，手機寬度檢視效果最佳。
- [ ] 通訊風險驗證（見下）
- [ ] 原生 App

### 看原型

```
cd ski-intercom/prototype && python3 -m http.server 8000
```

然後用手機或瀏覽器的手機模式開 `http://localhost:8000`。「模擬事件」面板可以觸發加入、離線、訊號變弱、改走接力，原型會同時顯示橫幅和提示音，對應真實 App 中耳機裡的語音提示。

## UI 設計原則

1. 幾乎不碰螢幕：手機在口袋、使用者戴手套。
2. 用聲音當主要回饋：加入、離線、訊號弱、靜音都用耳機提示音或語音。
3. 單一主畫面：隊員、訊號強弱、說話狀態、靜音、離開。
4. 暗色、高對比、大按鈕（最小點擊區 60 px）。
5. 設定在上纜車前完成：4 位數代碼或 QR code。
6. 狀態不只靠顏色：訊號用格數加文字，說話中用邊框加文字。

## 技術計畫

### 傳輸層

| 平台組合 | 技術 |
|---|---|
| 全 iPhone | Multipeer Connectivity |
| 全 Android | Nearby Connections（`P2P_CLUSTER`） |
| 混用 | Wi-Fi Aware 或 BLE L2CAP（較複雜） |

### 音訊

- Opus，12–24 kbps，20 ms 封包，延遲目標 < 200 ms
- 語音偵測（VAD）自動發話，不用按鍵
- 本機混音多路語音
- `AVAudioSession` `.playAndRecord` 加藍牙，AirPods 麥克風啟用時會降到通話音質

### Mesh

- 每支手機收到封包後帶 TTL 轉送（flooding），隊伍拉長時靠中間的人接力
- 自動重連、離線提示

## 已知風險（優先驗證）

1. **iOS 背景運作：** 鎖屏後 Multipeer 加音訊 session 能否持續通話。Apple 開發者論壇有人回報 Push to Talk 框架搭配免基礎設施連線會遇到嚴重的背景限制。
2. **距離：** Murmur 的使用者回報單跳約 20–50 m。要拉長可試 BLE Coded PHY（Android 可指定，iOS 目前不開放）或外接 LoRa。
3. **AirPods 音質：** 麥克風啟用後降到通話模式，有人回報多人都用藍牙耳機時音質變差。
4. **風噪與低溫：** VAD 門檻、電量。

## 相關產品

- Murmur（iOS / Android）：BLE mesh，最多 16 人，UI 與距離是主要缺點
- Talky、bitTalk、DirectTalk（iOS）：Multipeer 對講機
- mesh-walkie（Android，開源）：Nearby Connections
- Cardo PACKTALK、POC Obex Connect、EJEAS S2：安全帽硬體對講機
