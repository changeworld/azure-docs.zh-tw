---
title: 微軟 Azure 安全代碼分析任務自訂指南
description: 本文介紹了用於自訂 Microsoft 安全代碼分析擴展中所有任務的 YAML 配置選項
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 11/29/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 82802ceae8f9fdd24d1f5642d80d90ebfd8b92a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460663"
---
# <a name="yaml-configuration-options-to-customize-the-build-tasks"></a>用於自訂生成任務的 YAML 配置選項

本文列出了每個生成任務中可用的所有 YAML 配置選項。 本文從安全代碼分析工具的任務開始。 它以後處理任務結束。

## <a name="anti-malware-scanner-task"></a>反惡意軟體掃描器任務

| **輸入類型**      | **類型**     | **適用**            | **必要** | **預設值**             | **選項（用於選取清單）**                                   | **描述**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 輸入類型 | 挑選清單 | always | True | 基本 | 基本，自訂 | 
| ScanType | 挑選清單 | 輸入類型 = 基本 | True | 自訂掃描 | 自訂掃描、全系統掃描、快速掃描、配置掃描 | 用於反惡意軟體掃描的掃描類型。
| 檔迪拉 | filePath | 掃描類型 = 自訂掃描 | True | $（生成.臨時目錄） |  | 指示要掃描的檔或目錄。
| 禁用修復 | boolean | 掃描類型 = 自訂掃描 | False | true |  | 選中時：1） 忽略檔排除項。 2） 掃描存檔檔。 3） 檢測後不應用操作。 4） 檢測後不會寫入事件日誌條目。 5） 自訂掃描中的檢測不會顯示在使用者介面中。 6） 主控台輸出將顯示來自自訂掃描的檢測清單。
| 開機磁區掃描 | boolean | 掃描類型 = 自訂掃描 | False | false |  | 如果選中，它支援開機磁區掃描。
| 引數 | 字串 | 輸入類型 = 自訂 | True | -掃描 - 掃描類型 3 -禁用修復 - 檔 $（生成.臨時目錄） |  | 命令列參數，其中 -File 的參數是絕對路徑，或生成代理上預定義的 $（Build.StagingDirectory） 的相對路徑。 注意：如果您未提供 -File 作為最後一個參數的參數，則預設為 $（Build.StagingDirectory）。 您還可以提供 MpCmdRun.exe 工具允許的您自己的參數。<br/><br/>有關此工具的命令列參數的更多詳細資訊，請輸入<strong>-h</strong>或<strong>-h？</strong> 在"參數"欄位中並執行生成任務。
| 啟用服務 | boolean | always | True | false |  | 如果選中，它將嘗試啟用 Windows 更新所需的服務，以防它們被禁用。<br/>**注意**：請確保群組原則不禁用服務，並且此生成下運行的帳戶具有管理員許可權。
| 支援登錄錯誤 | boolean | always | True | false |  | 如果選中，它將收集在發生錯誤時用於診斷的支援檔。 這可能需要幾分鐘的時間。<br/>**注意**：請確保此生成運行的帳戶具有管理員許可權。
| 處理簽名更新失敗as | 挑選清單 | always | True | 警告 | 錯誤， 標準， 警告 | 如果在運行時無法更新簽名，則使用的日誌級別。 當設置為**Error**時，更新簽名失敗將使生成任務失敗。 請注意，簽名更新在託管生成代理上失敗很常見，即使簽名可能相對最新（不到 3 小時）。
| 簽名新鮮度 | 挑選清單 | always | True | 截至 | 一天，三天，兩天，更新 | 反惡意軟體簽名的最大允許年齡。 如果簽名無法更新且早于此值，則生成任務將根據 **"驗證簽名年齡作為"** 欄位中的選定值執行。 注意：如果您選擇 **"最新"，** 則允許簽名最多 3 小時。
| 治療過時簽名 | 挑選清單 | always | True | 錯誤 | 錯誤， 標準， 警告 | 如果簽名年齡早于所選**的反惡意軟體簽名年齡**，則使用的日誌級別。 過期的簽名可能會被視為**警告**或**資訊，** 以繼續反惡意軟體掃描，但不建議這樣做。

## <a name="binskim-task"></a>賓斯基任務

| **輸入類型**      | **類型**     | **適用**            | **必要** | **預設值**             | **選項（用於選取清單）**                                   | **描述**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 輸入類型 | 挑選清單 | always | True | 基本 | 基本，命令線 | 
| 引數 | 字串 | 輸入類型 = 命令列 | True |  |  | 要執行的標準 Binskim 命令列參數。 將刪除並更換輸出路徑。<br>有關此工具的命令列參數的更多詳細資訊，請在"參數"欄位中輸入**説明**並執行生成任務。
| 函式 | 挑選清單 | 輸入類型 = 基本 | True | 分析 | 分析、轉儲、匯出配置、匯出規則 | 
| 分析目標 | filePath | 輸入類型 = 基本 && 函數 = 分析 | True | $（生成.工件暫存\*目錄） .dll;<br>$（生成.工件暫存\*目錄） .exe |  | 一個或多個檔、目錄或篩選器模式的指定者，該模式解析為一個或多個要分析的二進位檔案。 （''''分離名單）
| 分析SymPath | 字串 | 輸入類型 = 基本 && 函數 = 分析 | False |  |  | 目標符號檔的路徑。
| 分析配置路徑 | 字串 | 輸入類型 = 基本 && 函數 = 分析 | False | default |  | 策略檔的路徑，將用於配置分析。 傳遞"預設"值以使用內置設置。
| 分析插入路徑 | 字串 | 輸入類型 = 基本 && 函數 = 分析 | False |  |  | 將針對分析集中的所有目標調用的外掛程式路徑。
| 分析重詛咒 | boolean | 輸入類型 = 基本 && 函數 = 分析 | False | true |  | 在評估檔指定參數時，將重新詛咒為子目錄。
| 分析韋爾博斯 | boolean | 輸入類型 = 基本 && 函數 = 分析 | False | false |  | 發出冗長的輸出。 由此產生的全面報告旨在為合規性方案提供適當的證據。
| 分析雜湊斯 | boolean | 輸入類型 = 基本 && 函數 = 分析 | False | false |  | 輸出 SHA-256 分析目標雜湊，當發出 SARIF 報告時。
| 分析統計 | boolean | 輸入類型 = 基本 && 函數 = 分析 | False | false |  | 生成分析會話的計時和其他統計資訊。
| 分析環境 | boolean | 輸入類型 = 基本 && 函數 = 分析 | False | false |  | 日誌電腦環境運行到輸出檔案的詳細資訊。 警告： 此選項將潛在敏感資訊（如所有環境變數值）記錄到任何已發出的日誌。
| 匯出規則輸出類型 | 挑選清單 | 輸入類型 = 基本 && 函數 = 匯出規則 | False | 薩里夫 | 薩里夫， 聲納庫貝 | 要輸出的規則描述項檔的類型。 這將包含在發佈安全分析日誌生成任務發佈的 BinSkim 日誌資料夾中。
| 轉儲目標 | filePath | 輸入類型 = 基本 && 函數 = 轉儲 | True | $（生成.工件臨時目錄） |  | 一個或多個檔、目錄或篩選器模式的指定者，該模式解析為一個或多個要分析的二進位檔案。 （''''分離名單）
| 轉儲重新詛咒 | boolean | 輸入類型 = 基本 && 函數 = 轉儲 | False | true |  | 在評估檔指定參數時，將重新詛咒為子目錄。
| 轉儲韋爾博斯 | boolean | 輸入類型 = 基本 && 函數 = 轉儲 | False | true |  | 發出冗長的輸出。 由此產生的全面報告旨在為合規性方案提供適當的證據。
| 工具版本 | 挑選清單 | always | False | 最新 | 1.5.0， 最新， 最新預發佈 | 要運行的工具的版本。

## <a name="credential-scanner-task"></a>憑據掃描程式任務

| **輸入類型**      | **類型**     | **適用**            | **必要** | **預設值**             | **選項（用於選取清單）**                                   | **描述**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OutputFormat | 挑選清單 | always | False | pre | csv， 前， 茨夫 | 憑據掃描程式結果檔的輸出格式。
| 工具版本 | 挑選清單 | always | False | 最新 | 1.27.7， 最新， 最新發佈 | 要運行的工具的版本。
| 掃描資料夾 | filePath | always | False | $（生成.原始目錄） |  | 要掃描憑據的存儲庫中的資料夾。
| 搜索者檔案類型 | 挑選清單 | always | False | 預設 | 自訂、預設、預設和自訂 | 用於查找用於掃描的搜索器檔的選項。
| 搜索者檔 | filePath | 搜索者檔案類型 = 自訂或搜索者檔案類型 = 預設和自訂 | False |  |  | 憑據掃描程式搜索器設定檔的要執行的檢查。 通過提供與逗號分隔的路徑清單到憑據掃描程式搜索器檔，可以包含和使用多個值。
| 抑制檔 | filePath | always | False |  |  | 憑據掃描程式抑制檔用於抑制輸出日誌中的問題。
| 抑制錯誤 | boolean | always | False | false |  | 抑制的匹配將輸出到輸出檔案 [-O] 匹配。[-f] 而不是預設的抑制輸出檔案 [-O] 抑制。[-f]。 （預設值為"False"）
| 詳細輸出 | boolean | always | False | false |  | 輸出詳細資訊。
| batchSize | 字串 | always | False |  |  | 用於並行運行憑據掃描器的併發執行緒數。 （預設為 20）<br/>值必須在 1-2147483647 的範圍內。
| 正則運算式匹配時間以秒為單位 | 字串 | always | False |  |  | 放棄檢查之前嘗試搜索器匹配的時間（以秒為單位）。<br/>添加到``-Co RegexMatchTimeoutInSeconds=<Value>``命令列。
| 檔Scanread緩衝區大小 | 字串 | always | False |  |  | 讀取以位元組為單位的內容時的緩衝區大小。 （預設值為 524288）<br/>添加到``-Co FileScanReadBufferSize=<Value>``命令列。
| 最大檔掃描位元組 | 字串 | always | False |  |  | 在內容分析期間，從給定檔讀取的最大位元組數。 （預設值為 104857600）<br/>添加到``-Co MaxFileScanReadBytes=<Value>``命令列。

## <a name="microsoft-security-risk-detection-task"></a>微軟安全風險檢測任務

| **輸入類型**      | **類型**     | **適用**            | **必要** | **預設值**             | **選項（用於選取清單）**                                   | **描述**                                                                                                                                                                                                                                                                           |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 服務終結點名稱 | 連接的服務：通用 | always | True |  |  | 存儲 MSRD 實例 URL（已已加入）和 REST API 訪問權杖（從您的帳戶設置頁生成並允許完全訪問您的帳戶）的 VSTS 專案中的預佈建服務終結點（通用類型）的名稱。
| AccountId | 字串 | always | True |  |  | 識別帳戶的 GUID。 可以從帳戶 URL 檢索它。
| 二進位 URL | 字串 | always | True |  |  | 模糊電腦用於下載二進位檔案的 URL 的分號分隔清單。 確保 URL 是公開的。
| 種子URL | 字串 | always | False |  |  | 模糊機用於下載種子的 URL 的分號分隔清單。 確保 URL 是公開的。
| OSPlatform類型 | 挑選清單 | always | True | Windows | Linux、Windows | 要運行模糊作業的作業系統平臺類型的電腦。
| 視窗版 | 字串 | OSPlatform 類型 + 視窗 | True | Server 2008 R2 |  | 運行模糊作業的作業系統版本的電腦。
| LinuxEdition | 字串 | OSPlatform類型 + Linux | True | Redhat |  | 運行模糊作業的作業系統版本的電腦。
| 預提交命令 | 字串 | always | False |  |  | 在提交模糊作業之前，需要在測試電腦上運行以安裝測試目的程式及其依賴項的腳本。
| 種子目錄 | 字串 | always | True |  |  | 包含種子的模糊電腦上目錄的路徑。 有關詳細資訊，請參閱[種子檔目錄](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/03-choose-seed-files#seed-file-directory)。
| 種子擴展 | 字串 | always | True |  |  | 種子的檔副檔名。
| 測試驅動程式可執行 | 字串 | always | True |  |  | 到模糊電腦上的目標可執行檔的路徑。 有關詳細資訊[，請參閱 EPE 的完整路徑](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/02-choose-exe#full-path-to-the-epe)。
| 測試驅動程式Exe類型 | 挑選清單 | always | True | x86 | amd64， x86 | 目標可執行檔體系結構。
| 測試驅動程式參數 | 字串 | always | True | "%測試檔案%" |  | 傳遞給測試目標可執行檔的命令列參數。 請注意 **，"%testfile%"** 符號（包括雙引號）將自動替換為測試驅動程式要解析的目的檔案的完整路徑，並且是必需的。 有關詳細資訊，請參閱[命令列參數](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/02-choose-exe#command-line-arguments)。
| 關閉自身 | boolean | always | True | true |  | 檢查測試驅動程式是否在完成時自行終止（例如，測試驅動程式解析輸入檔並立即退出）;取消選中是否需要強制關閉測試驅動程式（例如，測試驅動程式是一個 GUI 應用程式，其主視窗在分析輸入後保持打開狀態）。 有關詳細資訊[，請參閱自終止](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/05-scope-exe-lifetime#self-termination)。
| 最大持續時間（秒） | 字串 | always | True | 5 |  | 測試驅動程式的最大持續時間（以秒為單位）。 提供目的程式解析輸入檔所需的最長合理預期時間的估計。 此估計越準確，模糊運行的效率就越高。 有關詳細資訊，請參閱[最大預期執行持續時間](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/05-scope-exe-lifetime#maximum-expected-execution-duration)。
| 坎倫重複 | boolean | always | True | true |  | 檢查測試驅動程式是否可以重複運行，而無需根據持久/共用的全域狀態運行。 有關詳細資訊，請參閱[從頭開始運行](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/04-describe-exe-characteristics#runs-from-scratch)。
| 可測試驅動程式重新命名 | boolean | always | True | false |  | 檢查測試驅動程式可執行檔是否可以重命名，並且是否仍可以正常工作。 有關詳細資訊[，請參閱可重命名和並行化](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/04-describe-exe-characteristics#can-be-renamed-and-parallelized)。
| 單一程式 | boolean | always | True | false |  | 檢查測試驅動程式是否在單個作業系統進程下運行;如果測試驅動程式在單個作業系統進程下運行。取消選中測試驅動程式是否生成其他進程。 有關詳細資訊，請參閱[單一進程](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/04-describe-exe-characteristics#single-process)。

## <a name="roslyn-analyzers-task"></a>羅斯林分析器任務

| **輸入類型**      | **類型**     | **適用**            | **必要** | **預設值**             | **選項（用於選取清單）**                                   | **描述**                                                                                                                                                                                                                                                                                                                   |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 使用者提供構建資訊 | 挑選清單 | always | True | 自動 | 自動， msBuildInfo | 使用者提供 MSBuild 版本、MSBuild 體系結構和構建命令列以進行 Roslyn 分析的選項。 如果選擇了 **"自動"，** 則此任務將從同一管道中的上一個**MSBuild** **、VSBuild**和/或 **.NET Core（** 用於生成）任務中檢索生成資訊。
| msBuildVersion | 挑選清單 | 使用者提供構建資訊 = ms 構建資訊 | True | 16.0 | 15.0, 16.0 | MSBuild 版本。
| ms 構建架構 | 挑選清單 | 使用者提供構建資訊 = ms 構建資訊 | True | x86 | 點網核心， x64， x86 | MSBuild 體系結構。 注意：如果生成命令列調用**dotnet.exe 生成**，請選擇**Via .Net Core**選項。
| ms 構建命令線 | 字串 | 使用者提供構建資訊 = ms 構建資訊 | True |  |  | 用於編譯解決方案或專案的完整生成命令線。<br/><br/>注意：命令列應以**MSBuild.exe**或**dotnet.exe**的完整路徑開頭。<br/>該命令將以 $（生成.SourcesDirectory） 作為工作目錄運行。
| 規則集名稱 | 挑選清單 | always | False | 建議 | 自訂，無，推薦，必需 | 要使用的命名規則集。<br/><br/>如果`Ruleset Configured In Your Visual Studio Project File(s)`選擇，將使用在 VS 專案檔案中預配置的規則集。 如果`Custom`選擇，則可以設置自訂規則集路徑選項。
| 規則集版本 | 挑選清單 | 規則集名稱 = 必需或規則集名稱 = 推薦 | False | 最新 | 8.0， 8.1， 8.2， 最新， 最新發佈 | 所選 SDL 規則集的版本。
| 自訂規則集 | 字串 | 規則集名稱 = 自訂 | False |  |  | 要使用的規則集的可訪問路徑。 相對路徑將正常化為源存儲庫的根目錄 （`$(Build.SourcesDirectory)`。<br/><br/>如果規則集指定`Rules`設置為`Actions` `Error`，則生成任務將失敗。 要使用這樣做的規則集，請簽入`Continue on error`生成任務的 。 `Control Options`
| 微軟分析器版本 | 挑選清單 | always | False | 最新 | 2.9.3， 2.9.4， 2.9.6， 最新， 最新發佈 | 要運行的[微軟.代碼分析.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)套裝軟體的版本。
| 抑制檔編譯器警告 | filePath | always | False |  |  | 禁止顯示 C# 和 VB 編譯器警告的抑制檔。<br/><br/>帶有每個警告 ID 的純文字檔列出了一條單獨的行。<br/>對於編譯器警告，請僅指定警告識別碼的數位部分。 例如，1018 將抑制 CS1018，CA1501 將抑制 CA1501。<br/><br/>相對檔路徑將追加到源存儲庫的根目錄 （`$(Build.SourcesDirectory)`。

## <a name="tslint-task"></a>TSLint 任務

| **輸入類型**      | **類型**     | **適用**            | **必要** | **預設值**             | **選項（用於選取清單）**                                   | **描述**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 規則庫 | 挑選清單 | always | True | 茨林特 | 自訂， 微軟， 茨林特 | 所有結果都包括與所選版本的 TSLint（**僅限基本版本**）一起提供的規則。<br/><br/>**僅底座 -** 只有 TSLint 附帶的規則。<br/><br/>**包括微軟規則 -** 下載[tslint-microsoft-contrib，](https://github.com/Microsoft/tslint-microsoft-contrib)並包括其規則，可用於 TSLint 運行。 選擇此選項將隱藏`Type Checking`核取方塊，因為它是 Microsoft 規則要求並將自動使用的核取方塊。 它還取消隱藏`Microsoft Contribution Version`欄位，允許`tslint-microsoft-contrib`從[npm](https://www.npmjs.com/package/tslint-microsoft-contrib)中選擇 版本。<br/><br/>**包括自訂規則 -** 取消隱藏`Rules Directory`欄位，該欄位接受 TSLint 規則目錄的可訪問路徑，可用於 TSLint 運行。<br/><br/>**注：** 預設值已更改為 tslint，因為許多使用者在配置 Microsoft 規則集時遇到問題。 有關特定版本配置，請參閱[GitHub 上的 tslint-microsoft-contrib。](https://github.com/microsoft/tslint-microsoft-contrib)
| 規則目錄 | 字串 | 規則庫 = 自訂 | True |  |  | 包含其他 TSLint 規則的可訪問目錄，可用於 TSLint 運行。
| 規則集 | 挑選清單 | 規則庫 ！= 微軟 | True | ts推薦 | 自訂， tslatest， ts推薦 | 定義要針對 TypeScript 檔運行的規則。<br/><br/>**[茨林特：最新](https://github.com/palantir/tslint/blob/master/src/configs/latest.ts) - **擴展`tslint:recommended`並不斷更新，以在每個 TSLint 版本中包括最新規則的配置。 由於啟用了導致代碼中絨毛故障的新規則，使用此配置可能會跨次要版本引入重大更改。 當 TSLint 達到主要版本凹凸`tslint:recommended`時，將更新為 與`tslint:latest`相同。<br/><br/>**[茨林特：推薦](https://github.com/palantir/tslint/blob/master/src/configs/recommended.ts) - **TSLint 鼓勵一套穩定、有點有意見的規則來程式設計一般 TypeScript 程式設計。 此配置遵循`semver`，因此*不會在*次要版本或修補程式版本中有重大更改。
| 規則集微軟 | 挑選清單 | 規則庫 = 微軟 | True | mssdl 要求 | 自訂， 推薦， mssdl 推薦， mssdl 要求， tssdl， tslatest， ts 推薦 | 定義要針對 TypeScript 檔運行的規則。<br/><br/>**[微軟：需要](https://github.com/Microsoft/tslint-microsoft-contrib/wiki/TSLint-and-the-Microsoft-Security-Development-Lifecycle) -sdl**運行 tslint 和 tslint-microsoft-contrib 規則提供的所有可用檢查，以滿足*所需的*[安全開發生命週期 （SDL）](https://www.microsoft.com/sdl/)策略。<br/><br/>**[微軟：sdl 推薦](https://github.com/Microsoft/tslint-microsoft-contrib/wiki/TSLint-and-the-Microsoft-Security-Development-Lifecycle) - **運行 tslint 和 tslint-microsoft-contrib 規則提供的所有可用檢查，這些檢查滿足*所需的和推薦*[的安全開發生命週期 （SDL）](https://www.microsoft.com/sdl/)策略。<br/><br/>**[微軟：推薦](https://github.com/Microsoft/tslint-microsoft-contrib/blob/master/recommended_ruleset.js) - **tslint-microsoft-contrib 規則的建立者推薦的所有檢查。 這包括安全和非安全檢查。<br/><br/>**[茨林特：最新](https://github.com/palantir/tslint/blob/master/src/configs/latest.ts) - **擴展`tslint:recommended`並不斷更新，以在每個 TSLint 版本中包括最新規則的配置。 由於啟用了導致代碼中絨毛故障的新規則，使用此配置可能會跨次要版本引入重大更改。 當 TSLint 達到主要版本凹凸`tslint:recommended`時，將更新為 與`tslint:latest`相同。<br/><br/>**[茨林特：推薦](https://github.com/palantir/tslint/blob/master/src/configs/recommended.ts) - **TSLint 鼓勵一套穩定、有點有意見的規則來程式設計一般 TypeScript 程式設計。 此配置遵循`semver`，因此*不會在*次要版本或修補程式版本中有重大更改。
| 規則集檔 | 字串 | 規則集 = 自訂或規則集微軟 = 自訂 | True |  |  | 指定要運行的規則的[設定檔](https://palantir.github.io/tslint/usage/cli/)。<br/><br/>配置的路徑將添加為[自訂規則的](https://palantir.github.io/tslint/develop/custom-rules/)路徑。
| 檔選擇類型 | 挑選清單 | always | True | 檔Glob | 檔Glob，專案檔案 | 
| 檔案 | 字串 | 檔選擇類型 = 檔 Glob | True | **\*.ts |  | 決定要處理的檔的 file [glob。](https://www.npmjs.com/package/glob) 路徑與`Build.SourcesDirectory`值相關。<br/><br/>Microsoft 的"貢獻"庫需要使用專案檔案。 如果將 Microsoft 的"貢獻"庫與`File Glob Pattern`選項一起使用，將為您生成專案檔案。
| ECMAScript 版本 | 挑選清單 | 檔選擇類型 = 檔Glob && 規則庫 = 微軟 | True | ES3 | ES2015、 ES2016、 ES2017、 ES3、 ES5、 ES6、 ESNext | 使用 TypeScript 編譯器配置的 ECMAScript 的目標版本。 使用專案檔案時，這是 TypeScript tsconfig.json 檔的編譯器選項.target 欄位。
| 隨附此逐步解說的專案 | 字串 | 檔選擇類型 = 專案檔案 | True |  |  | 指定要運行 TSLint 的 TypeScript 檔的[tsconfig.json](http://www.typescriptlang.org/docs/handbook/tsconfig-json.html)檔的路徑。 路徑與`Build.SourcesDirectory`值相關。
| 類型檢查 | boolean | 規則庫 ！= 微軟 && 檔選擇類型 = 專案檔案 | False | true |  | 運行林排規則時啟用類型檢查器。
| 排除檔 | 字串 | always | False |  |  | 一個[glob，](https://www.npmjs.com/package/glob)指示要從林丁中排除的檔。 路徑與`Build.SourcesDirectory`值相關。 可以用分號分隔多個值。
| OutputFormat | 挑選清單 | always | True | json | 檢查風格， 代碼框架， 檔案清單， json， msbuild， pmd， 散文， 時尚， 冗長， vso | 用於生成輸出[的要用的事項](https://palantir.github.io/tslint/formatters/)。 請注意，JSON 格式與後分析相容。
| 節點記憶體 | 字串 | always | False |  |  | 用於運行 TSLint 的 MB 中用於分配到節點的顯式記憶體量。 示例： 8000<br/><br/>映射到節點的`--max_old_space=<value>`CLI 選項，該選項為`v8 option`。
| 工具版本 | 挑選清單 | 規則庫 ！= 微軟 | True | 最新 | 4.0.0、 4.0.1、 4.0.2、 4.1.0、 4.1.1、 4.2.0、 4.3.0、 4.3.1、 4.4.0、 4.4.1、 4.4.2、 4.5.0、 4.5.1、 5.0.0、 5.1.0、 5.2.0、 5.3.0、 5.4.2、 5.4.1、 5.4.2、 5.4.3、 5.5.0、 5.5.0、 5.4.0、 5.4.0、 5.4.1、 5.4.0、 5.4.0、 5.4.1、 5.4.0、 5.4.3、 5.5.0、 | 要下載並運行的 TSLint[版本](https://github.com/palantir/tslint/releases)。
| 類型腳本版本 | 挑選清單 | always | True | 最新 | 0.8.0, 0.8.1, 0.8.2, 0.8.3, 0.9.0, 0.9.1, 0.9.5, 0.9.7, 1.0.0, 1.0.1, 1.3.0, 1.4.1, 1.5.3, 1.6.2, 1.7.3, 1.7.5, 1.8.0, 1.8.10, 1.8.2, 1.8.5, 1.8.6, 1.8.7, 1.8.9, 1.9.0, 2.0.0, 2.0.10, 2.0.2, 2.0.3, 2.0.6, 2.0.7, 2.0.8, 2.0.9, 2.1.1, 2.1.4, 2.1.5, 2.1.6, 2.2.0,2.2.1， 自訂， 最新 | 要下載和使用[的類型記的版本](https://www.npmjs.com/package/typescript)。<br/>**注：** 這必須是與編譯代碼所用的 TypeScript 版本相同。
| 類型腳本版本自訂 | 字串 | 類型腳本版本 = 自訂 | True | 最新 |  | 要下載和使用[的類型記的版本](https://www.npmjs.com/package/typescript)。<br/>**注：** 這必須是與編譯代碼所用的 TypeScript 版本相同。
| 微軟公司 | 挑選清單 | 規則庫 = 微軟 |  | 最新 | 4.0.0、 4.0.1、 5.0.0、 5.0.1， 最新 | 要下載和使用[tslint-microsoft-contrib（SDL](https://www.npmjs.com/package/tslint-microsoft-contrib)規則）的版本。</br>**注：**[將](https://www.npmjs.com/package/tslint)選擇與為 tslint-microsoft-contrib 選擇的版本相容的版本。 此生成任務將封閉對 tslint-microsoft-contrib 的更新，直到可以進行一段時間的測試。

## <a name="publish-security-analysis-logs-task"></a>發佈安全分析日誌任務

| **輸入類型**      | **類型**     | **適用**            | **必要** | **預設值**             | **選項（用於選取清單）**                                   | **描述**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 專案名稱 | 字串 | always | True | 代碼分析日誌 |  | 要創建的專案的名稱。
| 專案類型 | 挑選清單 | always | True | 容器 | 容器，檔路徑 | 要創建的專案的類型。
| TargetPath | 字串 | 專案類型 = 檔路徑 | False | \\我的共用\$（生成.定義名稱）<br>\$（生成.生成編號） |  | 要複製到的檔共用
| 所有工具 | boolean | always | True | true |  | 發佈由所有安全開發工具生成任務生成的結果。
| 反惡意軟體 | boolean | 所有工具 = 錯誤 | True | true |  | 發佈由反惡意軟體生成任務生成的結果。
| 賓斯基姆 | boolean | 所有工具 = 錯誤 | True | true |  | 發佈由 BinSkim 生成任務生成的結果。
| 克雷斯坎 | boolean | 所有工具 = 錯誤 | True | true |  | 發佈由憑據掃描程式生成任務生成的結果。
| MSRD | boolean | 所有工具 = 錯誤 | True | true |  | 發佈 MSRD 生成任務啟動的 MSRD 作業的作業資訊和作業 URL。 MSRD 作業執行時間長，並提供單獨的報告。
| 羅斯林分析器 | boolean | 所有工具 = 錯誤 | True | false |  | 發佈由羅斯林分析器生成任務生成的結果。
| 茨林特 | boolean | 所有工具 = 錯誤 | True | true |  | 發佈由 TSLint 生成任務生成的結果。 請注意，報表僅支援 JSON 格式的 TSLint 日誌。 如果您選擇了其他格式，請相應地更新您的 TSLint 生成任務。
| 工具日誌未找到操作 | 挑選名單 | always | True | 標準 | 錯誤， 無， 標準， 警告 | 找不到所選工具的日誌時執行的操作（如果選中了所有工具，則執行任何工具），這意味著該工具未運行。<br/><br/>**選項：**<br/>**無：** 消息寫入詳細輸出流，只能通過將 VSTS 變數**系統設置為** **true**來訪問。<br/>**標準：（** 預設）寫入一個標準輸出消息，該消息未找到該工具的日誌。<br/>**警告：** 寫入一條黃色警告訊息，指出該工具未找到日誌，該工具在生成摘要頁上顯示為警告。<br/>**錯誤：** 寫入紅色錯誤訊息並引發異常，破壞生成。 使用此選項可確保使用單個工具選項來確保運行哪些工具。

## <a name="security-report-task"></a>安全報告工作

| **輸入類型**      | **類型**     | **適用**            | **必要** | **預設值**             | **選項（用於選取清單）**                                   | **描述**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VstsConsole | boolean | always | False | true |  | 將結果寫入管道主控台。
| 茨夫檔 | boolean | always | False | true |  | 生成 tsv 檔（選項卡分隔值），每個找到的結果有一行，並生成分隔結果資訊的選項卡。
| HtmlFile | boolean | always | False | true |  | 生成 html 報告檔。
| 所有工具 | boolean | always | True | false |  | 報告由所有安全開發工具生成任務生成的結果。
| 賓斯基姆 | boolean | 所有工具 = 錯誤 | True | false |  | 報告 BinSkim 生成任務生成的結果。
| 賓斯基基·魯蒙 | 挑選清單 | 所有工具 = 真或 BinSkim = true | True | 錯誤 | 錯誤，警告上方 | 要報告的結果級別。
| 克雷斯坎 | boolean | 所有工具 = 錯誤 | True | false |  | 報告由憑據掃描程式生成任務生成的結果。
| MSRD | boolean | 所有工具 = 錯誤 | True | false |  | 報告 MSRD 生成任務啟動的 MSRD 作業的作業資訊和作業 URL。 MSRD 作業執行時間長，並提供單獨的報告。
| 羅斯林分析器 | boolean | 所有工具 = 錯誤 | True | false |  | 報告由羅斯林分析器生成任務生成的結果。
| 羅斯林分析器突破 | 挑選清單 | 所有工具 = 真或羅斯林分析器 = 真 | True | 錯誤 | 錯誤，警告上方 | 要報告的結果級別。
| 茨林特 | boolean | 所有工具 = 錯誤 | True | false |  | 報告由 TSLint 生成任務生成的結果。 請注意，報表僅支援 JSON 格式的 TSLint 日誌。 如果您選擇了其他格式，請相應地更新您的 TSLint 生成任務。
| 茨林特·薩隆 | 挑選清單 | 所有工具 = 真或 TSlint = 真 | True | 錯誤 | 錯誤，警告上方 | 要報告的結果級別。
| 工具日誌未找到操作 | 挑選名單 | always | True | 標準 | 錯誤， 無， 標準， 警告 | 找不到所選工具的日誌時執行的操作（如果選中了所有工具，則執行任何工具），這意味著該工具未運行。<br/><br/>**選項：**<br/>**無：** 消息寫入詳細輸出流，只能通過將 VSTS 變數**系統設置為** **true**來訪問。<br/>**標準：（** 預設）寫入一個標準輸出消息，該消息未找到該工具的日誌。<br/>**警告：** 寫入一條黃色警告訊息，指出該工具未找到日誌，該工具在生成摘要頁上顯示為警告。<br/>**錯誤：** 寫入紅色錯誤訊息並引發異常，破壞生成。 使用此選項可確保使用單個工具選項來確保運行哪些工具。
| 自訂日誌資料夾 | 字串 | always | False |  |  | 分析工具日誌所在的基本資料夾;在此路徑下，各個日誌檔將位於以每個工具命名的子資料夾中。

## <a name="post-analysis-task"></a>分析後任務

| **輸入類型**      | **類型**     | **適用**            | **必要** | **預設值**             | **選項（用於選取清單）**                                   | **描述**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 所有工具 | boolean | always | True | false |  | 如果任何 Microsoft 安全代碼分析生成任務發現任何問題，則中斷生成。
| 賓斯基姆 | boolean | 所有工具 = 錯誤 | True | false |  | 根據您選擇的"中斷打開"選項，如果發現任何 BinSkim 問題，則中斷生成。
| 賓斯基基·魯蒙 | 挑選清單 | 所有工具 = 真或 BinSkim = true | True | 錯誤 | 錯誤，警告上方 | 要中斷生成的問題級別。
| 克雷斯坎 | boolean | 所有工具 = 錯誤 | True | false |  | 如果發現任何憑據掃描程式問題，則中斷生成。
| 羅斯林分析器 | boolean | 所有工具 = 錯誤 | True | false |  | 如果發現任何羅斯林分析器問題，則中斷生成。
| 羅斯林分析器突破 | 挑選清單 | 所有工具 = 真或羅斯林分析器 = 真 | True | 錯誤 | 錯誤，警告上方 | 要中斷生成的問題級別。
| 茨林特 | boolean | 所有工具 = 錯誤 | True | false |  | 如果發現任何 TSLint 問題，則中斷生成。 請注意，只有 JSON 格式的 TSLint 日誌支援用於後期分析。 如果您選擇了其他格式，請相應地更新您的 TSLint 生成任務。
| 茨林特·薩隆 | 挑選清單 | 所有工具 = 真或 TSlint = 真 | True | 錯誤 | 錯誤，警告上方 | 要中斷生成的問題級別。
| VstsConsole | boolean | always | False | true |  | 將結果寫入管道主控台。
| 工具日誌未找到操作 | 挑選名單 | always | True | 標準 | 錯誤， 無， 標準， 警告 | 找不到所選工具的日誌時執行的操作（如果選中了所有工具，則執行任何工具），這意味著該工具未運行。<br/><br/>**選項：**<br/>**無：** 消息寫入詳細輸出流，只能通過將 VSTS 變數**系統設置為** **true**來訪問。<br/>**標準：（** 預設）寫入一個標準輸出消息，該消息未找到該工具的日誌。<br/>**警告：** 寫入一條黃色警告訊息，指出該工具未找到日誌，該工具在生成摘要頁上顯示為警告。<br/>**錯誤：** 寫入紅色錯誤訊息並引發異常，破壞生成。 使用此選項可確保使用單個工具選項來確保運行哪些工具。

## <a name="next-steps"></a>後續步驟

如果您對安全代碼分析擴展和所提供的工具有任何疑問，請查看[我們的常見問題頁面](security-code-analysis-faq.md)。