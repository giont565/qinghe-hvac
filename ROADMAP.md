# P&ID 可建構式編輯器 — 開發路線圖

> 從現有「AHU 互動 P&ID 單機展示」拓展為「廠務系統 P&ID 平台」
> 使用者可從元件庫拖放、切換預設系統模板、編輯廠牌型號、匯出 SVG / PDF / Niagara point list

---

## 1. 系統模板清單（12 個預設）

### A. HVAC 空調
| ID | 名稱 | 描述 | 關鍵元件 |
|----|------|------|---------|
| A1 | **AHU** ✅（已完成） | 行政辦公空調機 | DM / Filter / Coil / Fan / VFD / TT / PDT / SD / CO2 / FS / ΔP / MV / BA |
| A2 | **MAU** | 100% 外氣機（潔淨室、製程） | Pre-filter / 中效 / HEPA / Coil / 加濕器 / 風機 / VFD / 露點 sensor |
| A3 | **FCU** | 風機盤管（小區域） | Coil / Fan / 三段速控制 / 室溫 sensor / 二通閥 |
| A4 | **VAV** | 變風量分區控制 | VAV box / 重熱盤管 / 室溫 / 風量 sensor |
| A5 | **進排氣** | 一般作業區換氣 | SAF / EAF / 風量計 / CO2 監測 |

### B. 給排水
| ID | 名稱 | 描述 | 關鍵元件 |
|----|------|------|---------|
| B1 | **給水系統** | 高位水箱 + 加壓泵 | 蓄水池 / 高位水箱 / 加壓泵 ×2 / 壓力槽 / 水位 / 壓力 sensor |
| B2 | **排水系統** | 重力 / 排水泵 | 集水井 / 排水泵 / 浮球 / 逆止閥 |
| B3 | **化糞池** | 三段式 + 過濾床 | 三段沉澱槽 / 厭氧濾床 / 出水井 / 警示液位 |
| B4 | **廢水泵浦** | 沉沙井 + 雙泵備援 | 沉沙井 / 雙泵 / 液位 ×3 / 排砂閘門 / VFD |
| B5 | **生態景觀池** | 循環過濾 + 化學監控 | 循環泵 / 砂濾 / 活性碳 / UV / 加藥 / pH / ORP / 濁度 / 溶氧 |

### C. 消防 / 排煙
| ID | 名稱 | 描述 | 關鍵元件 |
|----|------|------|---------|
| C1 | **廁所排氣** | 多衛浴共管 | EAF / 主管 / 支管 / 重力風門 / 末端格柵 |
| C2 | **消防排煙** | 走道排煙、樓梯加壓、防煙閘門 | 排煙風機 / 加壓風機 / 防煙閘 / 火警連動 |

每個模板包含：完整 P&ID SVG、元件 BOM、設備規格 metadata、控制/警報邏輯、預設廠牌型號。

---

## 2. 元件分類（總計 ~50 個 schema）

### 2.1 設備殼 (Enclosures)
AHU casing · MAU housing · FCU housing · VAV box · Damper box · Pump house · Tank（給水 / 廢水 / 沉沙井 / 化糞池）· Pond enclosure · Filter box（袋式 / 板式 / HEPA）· Heat exchanger casing

### 2.2 動力與機械 (Mechanical)
Centrifugal fan（蝸殼）· Plug fan ✅ · Axial fan · Pump（離心 / 沉水 / 直線）· Compressor · Cooling tower · Chiller · Boiler · Heat exchanger（板式 / 殼管）· Coil（冷卻 / 加熱 / 預熱）✅

### 2.3 致動器 (Actuators)
Modulating valve（2-way ✅ / 3-way）· Damper ✅（parallel / opposed blade）· VFD ✅ · Solenoid valve · Motorized ball valve · Check valve · Pressure relief valve · Gate / Globe valve

### 2.4 感測器 (Sensors)
TT ✅ · PDT ✅ · SD ✅ · CO₂ ✅ · FS ✅ · ΔP ✅ · Level transmitter（float / 超音波 / radar）· Pressure transmitter · Flow meter（渦輪 / 電磁 / 超音波 / venturi）· 水質（pH / ORP / DO / 濁度）· Current/voltage · Vibration · BTU meter

### 2.5 控制器 (Controllers)
BA Controller ✅（Honeywell ARENA NX）· FACP（Fire Alarm Control Panel）· VFD drive ✅ · PLC · Local DDC

---

## 3. 路徑 / 訊號類型 (Pipe & Signal Catalog)

| 類型 | 用途 | 顏色 | 樣式 |
|---|---|---|---|
| OA / SA | 外氣 / 送風 ✅ | `--pipe-mau` #A78BFA | flow dash |
| RA | 回風 | mau dim 50% | flow dash |
| EA | 排風 | `--pipe-exh` #F472B6 | flow dash |
| CHWS / CHWR | 冰水進回 ✅ | #00D4FF / #0080FF | flow dash |
| CWS / CWR | 冷卻水進回 | `--pipe-cws` #34D399 / `--pipe-cwr` #10B981 | flow dash |
| HHWS / HHWR | 熱水進回 | red shades | flow dash |
| Steam | 蒸氣 | white | solid 粗 |
| Condensate | 凝結水 | grey | solid |
| DCW / PCW | 給水（家用 / 製程） | navy blue | solid |
| WW / SS | 廢水 / 污水 | brown | solid |
| SW | 雨水 | light blue | solid |
| Sensor → BA | 感測訊號 ✅ | `--sensor` #A3E635 | dash 2 2 |
| BA → Actuator | 控制命令 ✅ | `--actuator` #FB923C | dash 2 3 |
| Fire I/O | 火警訊號 ✅ | #F472B6 | dash 3 2 |
| Power | 電源 | yellow | solid 粗 |
| Modbus / BACnet | 通訊匯流排 | cyan | dash 5 1 |

---

## 4. 技術 Stack

- **資料**: JSON Schema(component library + system templates)
- **渲染**: Vanilla JS SVG renderer（無 framework 依賴）
- **互動**: Native DOM events + 既有 hover/lock 邏輯
- **匯出**: SVG → PNG via canvas · SVG → PDF via jsPDF · JSON 直接下載
- **儲存**: LocalStorage（短期）→ Github Gist API（共享）→ 後端 DB（長期）

---

## 5. 開發階段

### Phase 1 — Foundation（當前位置）
- ✅ AHU 互動 P&ID 完成
- ✅ Hover/Lock 邏輯
- ✅ 拖元件 + 拖 path 端點編輯器
- ✅ Hover 廠牌資訊卡（Honeywell 新版 + Lucide icon）
- ✅ 左側功能卡 + 右側 P&ID + Detail panel 切換
- ⬜ 抽出 component library 成獨立 JSON（current SVG → schema）
- ⬜ 把現有 AHU 元件 migrate 進 library

### Phase 2 — Templates
- ⬜ 建立 12 個系統 JSON 模板
- ⬜ SVG renderer：吃 template JSON 渲染圖
- ⬜ 「系統切換」UI（下拉 / tab）
- ⬜ 每個系統獨立的 control loops + detail panels

### Phase 3 — Editor
- ⬜ Component palette（左 sidebar，可搜尋）
- ⬜ 拖放新增元件到畫布
- ⬜ 兩元件點選自動連線（含 path type 選擇）
- ⬜ Property inspector（右 sidebar）— 編輯元件廠牌/型號/setpoint
- ⬜ 儲存 / 載入 JSON
- ⬜ Undo / Redo

### Phase 4 — Extensions
- ⬜ 多圖紙（multi-sheet workbook）
- ⬜ 設備 BOM 自動產出
- ⬜ Niagara point list 匯出（BMS 工程直用）
- ⬜ 動態模擬（simulated values, alarm states）
- ⬜ Cloud sync（多人協作）

---

## 6. JSON Schema 範例

### 6.1 元件實例
```json
{
  "id": "fan-001",
  "type": "fan-plug",
  "category": "mechanical",
  "x": 450,
  "y": 240,
  "props": {
    "manufacturer": "EBM-PAPST",
    "model": "K3G560 RadiPac",
    "rating": "0.55 m³/s @ 1500 Pa",
    "power": "11 kW"
  },
  "anchors": {
    "airIn":  { "x": -32, "y": 0 },
    "airOut": { "x":  32, "y": 0 },
    "power":  { "x":   0, "y": 32 }
  }
}
```

### 6.2 系統模板
```json
{
  "meta": {
    "id": "b-ahu",
    "name": "B 棟 AHU",
    "dwgNo": "B-AHU-XF",
    "rev": "03",
    "date": "2026-05-12"
  },
  "viewBox": "0 0 900 520",
  "components": [
    { "id": "casing", "type": "ahu-casing", "x": 200, "y": 160, "w": 540, "h": 160 },
    { "id": "fan",    "type": "fan-plug",   "x": 450, "y": 240 },
    { "id": "vfd",    "type": "vfd",        "x": 420, "y": 385 }
  ],
  "connections": [
    { "type": "OA",      "from": "_outside",   "to": "dm.airIn" },
    { "type": "CHWS",    "from": "_chiller",   "to": "coil.chwsIn" },
    { "type": "signal",  "from": "pdt.bus",    "to": "ba.in1" },
    { "type": "command", "from": "ba.out1",    "to": "vfd.cmd" }
  ],
  "controlLoops": [
    {
      "id": "static",
      "name": "SA 風管靜壓",
      "sensor": "pdt",
      "actuator": "vfd",
      "algorithm": "PI",
      "setpoints": { "day": 250, "night": 150, "unit": "Pa" }
    }
  ]
}
```

---

## 7. 下一步 — 建議順序

1. **抽 component library**（半天）— 把現有 AHU 元件抽成 schema，每個元件含 symbol（SVG path 字串）+ anchor points + 預設廠牌
2. **寫 SVG renderer 雛形**（1 天）— 吃 schema 渲染元件、自動 routing 連線（基礎 L 形或 manhattan）
3. **建第二個 template**（半天）— 挑簡單的（建議 **C1 廁所排氣** 或 **A3 FCU**）驗證 renderer 是否能跑
4. **加「系統選擇器」UI**（半天）— 下拉切換目前展示的系統
5. **完成剩 10 個系統 JSON**（依複雜度 1~2 天每個）
6. **拖放編輯器**（1 週）

每步驟可獨立 commit，user 可看到 progressive 成果。

---

## 8. 注意事項

- 既有 AHU SVG 仍保留 hardcoded 路徑（已調整到完美狀態），renderer 完成後再 migrate
- 廠牌型號預設 Honeywell（HVAC 控制系統）+ Notifier（火警）+ KSB/Grundfos（水泵）+ Schneider/ABB（電氣）
- 路徑連線自動 routing 採 orthogonal（垂直 + 水平段）為主，避免斜線
- 所有圖紙統一深色主題（黑底 + 細白邊 + 彩色管路）

---

最終目標：**廠務工程師可在 30 分鐘內畫好一張新系統 P&ID 並交付 BMS 整合用 Niagara point list**。
