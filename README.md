# Mac 雙系統安裝 Windows 11 指南

本文件提供詳細步驟說明，協助使用者於 Mac 上安裝 Windows 11 雙系統。本文整合安裝所需工具與操作步驟，並補充各項實際操作細節，以確保整個安裝流程順暢且具參考價值。

## 目錄

- [Mac 雙系統安裝 Windows 11 指南](#mac-雙系統安裝-windows-11-指南)
  - [目錄](#目錄)
  - [前置作業與準備](#前置作業與準備)
  - [步驟一：安裝 Homebrew 與 wimlib](#步驟一安裝-homebrew-與-wimlib)
  - [步驟二：分割 install.wim 檔案](#步驟二分割-installwim-檔案)
  - [步驟三：製作 win.cdr 映像檔](#步驟三製作-wincdr-映像檔)
  - [步驟四：轉換 CDR 檔為 ISO 檔](#步驟四轉換-cdr-檔為-iso-檔)
  - [附加步驟：使用 Boot Camp 助理安裝 Windows 11](#附加步驟使用-boot-camp-助理安裝-windows-11)
  - [注意事項](#注意事項)
  - [授權與貢獻](#授權與貢獻)

## 前置作業與準備

- **硬體需求：**   
   - 確認磁碟空間充足，建議 Windows 分割區至少預留 64GB。依使用設備而定。

- **取得 Windows 安裝檔案：**  
   - 下載 [Windows 10](https://www.microsoft.com/zh-tw/software-download/windows10) 安裝媒體，並確認 `.ios` 檔案已下載完畢。 
   - 下載 [Windows 11](https://www.microsoft.com/zh-tw/software-download/windows11) 安裝媒體，並確認 `.ios` 檔案已下載完畢。 

- **❗準備 Windows 安裝資料夾：**  
   1. 建立一個新的資料夾 `win11` ，將 Windows 10 的 `.ios` 檔案開啟，並**複製**所有資料 (`source` 資料夾除外)。
   2. 在 `win11` 資料夾中創建新的 `source` 資料夾，將 Windows 10 的 `.ios` 檔案開啟，並**複製** `source` 資料夾中所有資料 (`install.wim` 檔案除外)。
   3. 將  Windows 11 的 `.ios` 檔案開啟，進入 `source` 資料夾，**複製** `install.wim` 檔案至桌面。
   4. 接續下面步驟。

## 步驟一：安裝 Homebrew 與 wimlib

1. **安裝 Homebrew：**  
   - 開啟「終端機」（Terminal）。  
   - 參閱 [Homebrew 官網](https://brew.sh/) 取得最新安裝指令，並於終端機中執行以下命令：
     ```bash
     /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
     ```
   - 根據提示輸入 mac 密碼並完成安裝。

2. **安裝 wimlib：**  
   - 在終端機中執行下列命令：
     ```bash
     brew install wimlib
     ```
   - wimlib 為後續分割 `install.wim` 檔案的必要工具。

## 步驟二：分割 install.wim 檔案

由於 Windows 安裝檔案可能容量過大（超過 FAT32 單一檔案限制），必須將 `install.wim` 分割為多個較小檔案。

1. **確認檔案位置：**  
   - 請將 `install.wim` 檔案放置於桌面（`~/Desktop`）。

2. **執行分割操作：**  
   - 在終端機中執行下列指令：
     ```bash
     cd ~/Desktop
     wimlib-imagex split install.wim install.swm 3500
     ```
   - 說明：  
     - 此命令將 `install.wim` 分割成每片約 3500 MB 的檔案，依序產生 `install.swm`、`install2.swm` 等檔案。
3. **分割後操作：** 
   - 將 `install.swm` 與 `install2.swm` 檔案移至 `~/Desktop/win11/source` 資料夾中。

## 步驟三：製作 win.cdr 映像檔

透過 mac 磁碟工具程式將 Windows 安裝資料夾轉換成 CDR 映像檔，以便後續 ISO 檔轉換。

1. **確認 Windows 安裝資料夾：**  
   - 請確認 `win11` 資料夾中包含所有 Windows 安裝所需檔案（包含分割後的 `.swm` 檔案）。

2. **利用磁碟工具程式建立 CDR 映像檔：**  
   - 開啟「磁碟工具程式」（Disk Utility）。  
   - 從選單中選擇「檔案」 > 「建立新映像檔」 > 「從資料夾建立映像檔…」。  
   - 選取 `win11` 資料夾後，請依以下設定：
     - **映像檔名稱：** `win11.cdr`
     - **儲存位置：** 建議儲存於桌面  
     - **映像格式：** 選擇「讀取/寫入」或「壓縮」
     - **檔案格式：** 選擇 DVD/CD 主映像檔（`.cdr`）
   - 點選「儲存」後，等待映像檔建立完成。

## 步驟四：轉換 CDR 檔為 ISO 檔

由於部分安裝工具（例如 Boot Camp 助理）僅接受 ISO 格式，故需將 `win11.cdr` 轉換為 ISO 檔。

1. **執行 ISO 轉換命令：**  
   - 確認 `win11.cdr` 檔案位於桌面，並在終端機中執行：
     ```bash
     cd ~/Desktop
     hdiutil makehybrid -iso -joliet -o win.iso win.cdr
     ```
   - 此命令將 `win11.cdr` 映像檔轉換成 ISO 格式，並產生 `win11.iso`。

2. **檢查生成結果：**  
   - 確認桌面上已出現 `win11.iso` 檔案，即可作為 Windows 11 安裝媒體使用。

## 附加步驟：使用 Boot Camp 助理安裝 Windows 11

若選擇利用 [BootCamp](https://support.apple.com/zh-tw/106378) 助理安裝 Windows 11，可依下列步驟操作：

1. **啟動 Boot Camp 助理：**  
   - 前往「應用程式」 > 「工具程式」中找到並啟動 Boot Camp 助理。

2. **建立 Windows 安裝磁碟：**  
   - 在 Boot Camp 助理中選擇「建立 Windows 安裝磁碟」，並指定剛生成的 `win.iso` 檔案。  
   - 插入容量至少 8GB 的 USB 隨身碟作為安裝媒體。

3. **磁碟分割：**  
   - Boot Camp 助理會自動協助分割硬碟，請根據需求調整 Windows 與 macOS 的分割區大小。

4. **開始安裝：**  
   - 點選「開始」後，系統將重新啟動並進入 Windows 安裝流程，請依照指示選擇 Boot Camp 分割區並格式化後完成安裝。

## 注意事項

- **備份重要資料：**  
  於進行磁碟分割與系統安裝前，務必備份所有重要資料，避免資料遺失。

- **驅動程式安裝：**  
  Windows 安裝完成後，請依 Boot Camp 助理提示安裝相應驅動程式，以確保硬體功能正常運作。

- **路徑設定：**  
  如各步驟中所使用檔案位置與預設路徑有所不同，請根據實際情況調整命令中的路徑參數。

## 授權與貢獻

本儲存庫內容以 [MIT 授權條款](LICENSE) 進行授權，歡迎任何形式的修訂與貢獻。  
如發現錯誤或有改進建議，請透過 Issue 或 Pull Request 與我們聯繫，謝謝！

---

