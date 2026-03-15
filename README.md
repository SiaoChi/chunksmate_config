# chunksmate_config

ChunkMate App 的遠端版本控制設定檔。App 啟動時會從此 repo 抓取 `remote-config.json`，判斷是否需要更新。

## 檔案結構

```
public/
└── remote-config.json   # App 版本控制設定
```

---

## remote-config.json 欄位說明

```json
{
  "min_version": "1.0.0",
  "ios_min_version": "1.3.0",
  "android_min_version": "1.0.0",
  "ios_latest_version": "1.3.0",
  "android_latest_version": "1.0.0",
  "update_description": {
    "zh-TW": "...",
    "zh-CN": "...",
    "en": "...",
    "ja": "...",
    "ko": "..."
  }
}
```

| 欄位 | 說明 |
|------|------|
| `min_version` | 全平台最低支援版本（fallback，優先度低於平台專屬欄位） |
| `ios_min_version` | iOS 強制更新門檻。低於此版本 → 強制更新 modal，無法關閉 |
| `android_min_version` | Android 強制更新門檻。同上 |
| `ios_latest_version` | iOS 最新版本。低於此版本但高於 min → 軟性提示，可「稍後再說」 |
| `android_latest_version` | Android 最新版本。同上。iOS / Android 可各自獨立更新 |
| `update_description` | 更新說明文字，支援 5 種語言（`zh-TW`, `zh-CN`, `en`, `ja`, `ko`） |

---

## 更新行為

### 強制更新（Hard Update）
- 條件：`currentVersion < ios_min_version`（或 `android_min_version`）
- Modal 無法關閉，必須前往 App Store / Google Play 更新

### 軟性更新（Soft Update）
- 條件：`ios_min_version <= currentVersion < ios_latest_version`（Android 同理）
- Modal 顯示「稍後再說」按鈕
- **用戶 dismiss 後，同版本不再重複提示**（記錄在 AsyncStorage）
- 當 `ios_latest_version` 升到更新版本時，會再次提示

### 不提示
- 條件：`currentVersion >= ios_latest_version`（或 `android_latest_version`）

---

## 如何發版時更新設定

1. 確認新版本號（例如 `1.4.0`）
2. 更新 `remote-config.json` 對應平台欄位：
   - 一般新版：只更新 `ios_latest_version` 或 `android_latest_version`
   - 有重大 breaking change 需強制更新：同時更新 `ios_min_version` / `android_min_version`
3. 更新 `update_description` 各語言內容
4. commit，**等上架審核通過後再 push** → App 下次啟動即生效

### 範例：iOS 發布 1.4.0，軟性更新

```json
{
  "min_version": "1.0.0",
  "ios_min_version": "1.3.0",
  "android_min_version": "1.0.0",
  "ios_latest_version": "1.4.0",
  "android_latest_version": "1.0.0"
}
```

### 範例：iOS 發布 1.4.0，強制更新

```json
{
  "min_version": "1.0.0",
  "ios_min_version": "1.4.0",
  "android_min_version": "1.0.0",
  "ios_latest_version": "1.4.0",
  "android_latest_version": "1.0.0"
}
```

---

## Remote Config URL

```
https://raw.githubusercontent.com/SiaoChi/chunksmate_config/main/public/remote-config.json
```
