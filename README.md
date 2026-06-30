# 虛擬貨幣六脈神劍選幣系統

這是台股版選股系統的加密貨幣版本，保留原本的武俠式模組：

- 六脈神劍：短線轉折訊號
- 求敗九劍：偏空 / 轉弱訊號
- 十八漲：偏多 / 反彈 / 動能訊號
- 十絕陣：趨勢、量價、突破、跌破、K 線訊號
- 多空機率：綜合多空命中數推估方向機率
- 藏鋒榜：多方精選 / 空方警示 / 多空並列精篩
- 持倉追蹤：已買進幣種的多空風險、損益與賣出/贖回提示

> 本工具僅為技術面篩選輔助，不構成任何投資建議。加密貨幣波動極高，請自行控管風險。

## 1. 使用方式

### 方式 A：直接用瀏覽器即時抓 Binance K 線

1. 打開 `虛擬貨幣六脈神劍選幣系統.html`
2. 檢查交易對清單，例如：
   ```text
   BTCUSDT,ETHUSDT,SOLUSDT,BNBUSDT
   ```
3. 選擇 K 線週期：`1d`、`4h`、`1h`
4. 按「即時抓取並選幣」

若瀏覽器、地區或網路限制導致 Binance API 抓取失敗，請改用方式 B。

### 方式 B：離線 CSV 匯入，較穩定

1. 安裝 Python 3
2. 執行：
   ```bat
   download_top_crypto.bat
   ```
3. 產生：
   ```text
   crypto_history.csv
   ```
4. 回到 HTML，上傳 `crypto_history.csv`
5. 按「用 CSV 資料選幣」

## 2. CSV 欄位格式

```csv
symbol,base,quote,category,interval,date,open,high,low,close,volume,quote_volume,trades
BTCUSDT,BTC,USDT,主流幣,1d,2026-06-30 00:00:00,....
```

欄位說明：

- `symbol`：交易對，例如 BTCUSDT
- `base`：基礎幣，例如 BTC
- `quote`：計價幣，例如 USDT
- `category`：分類，例如 主流幣、公鏈、DeFi、Layer2
- `interval`：K 線週期
- `date`：K 線時間
- `open/high/low/close`：OHLC
- `volume`：成交量，以 base asset 計
- `quote_volume`：成交額，以 quote asset 計
- `trades`：成交筆數

## 3. BAT 檔說明

### `download_top_crypto.bat`

下載 24h quoteVolume 前 100 名 USDT 現貨交易對，日線 160 根 K 線。

### `download_major_crypto_4h.bat`

下載前 80 名 USDT 現貨交易對，4 小時線 240 根 K 線。

### 手動命令

```bat
python crypto_screener_downloader.py --top 100 --interval 1d --limit 160 --out crypto_history.csv --sleep 0.2
```

指定交易對：

```bat
python crypto_screener_downloader.py --symbols BTCUSDT,ETHUSDT,SOLUSDT --interval 4h --limit 240 --out crypto_history_4h.csv
```

## 4. 模組說明

### 六脈神劍

偏短線轉折，包含 KD 金叉 / 死叉、站上 / 跌破 MA5、收高 / 收低。

### 求敗九劍

偏空風險模組，包含 KD 轉弱、MACD 死叉、K 值連跌、跌破均線。

### 十八漲

偏多動能模組，包含低指標反彈、DM 金叉、量增、站上均線等。

### 十絕陣

新增 10 個趨勢量價指標：

1. 青龍列陣：多頭排列
2. 白虎伏殺：空頭排列
3. 回陽一線：均線上彎
4. 墜陰一線：均線下彎
5. 雷動九霄：價漲量增
6. 破軍下殺：價跌量增
7. 破雲出岫：突破 20 根 K 高點
8. 墜淵失守：跌破 20 根 K 低點
9. 赤焰長虹：長紅 K
10. 黑煞斷魂：長黑 K

### 藏鋒榜

用來把候選清單再次精篩，可選：

- 多方精選
- 空方警示
- 多空並列

支援條件：

- 多方機率下限
- 空方機率上限
- 共振分數下限
- 十絕陣多方 / 空方命中數
- 排除多空打架
- 只取前 N 名

## 5. 持倉追蹤

可輸入：

- 交易對
- 買進價
- 購買金額
- 數量
- 備註

若只填購買金額、不填數量，系統會用：

```text
數量 = 購買金額 / 買進價
```

提示包含：

- 續抱
- 觀察
- 減碼觀察
- 賣出 / 贖回警示

## 6. 常見問題

### Binance 抓不到怎麼辦？

請改用 Python downloader 產生 CSV 後匯入。這樣可避開部分瀏覽器 CORS、網路或地區限制。

### 是否需要 API key？

此版本使用 Binance Spot public market data，不需要 API key。

### 能不能換交易所？

可以，後續可擴充 OKX、Bybit、Coinbase，但 CSV 欄位維持一致即可匯入分析。
