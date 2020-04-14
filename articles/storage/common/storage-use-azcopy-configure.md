---
title: 使用 Azure 儲存配置、優化和排除 AzCopy 問題 |微軟文件
description: 配置、優化和排除 AzCopy 故障。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 87a335f44a31436de735395adbee9035493cbbd2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263415"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>對 AzCopy 進行設定、最佳化及疑難排解

AzCopy 是一個命令列實用程式,可用於將 Blob 或檔案複製到存儲帳戶或從存儲帳戶複製。 本文可説明您執行進階設定任務,並説明您解決使用 AzCopy 時可能出現的問題。

> [!NOTE]
> 如果您要尋找說明您開始使用 AzCopy 的內容,請參閱以下任何文章:
> - [開始使用 AzCopy](storage-use-azcopy-v10.md)
> - [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)
> - [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
> - [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>進行 Proxy 設定

要配置 AzCopy 的代理設定`https_proxy`,請設定環境變數。 如果在 Windows 上運行 AzCopy,AzCopy 會自動檢測代理設定,因此您不必在 Windows 中使用此設定。 如果選擇在 Windows 中使用此設定,它將覆蓋自動檢測。

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | 在命令提示字元中，請使用：`set https_proxy=<proxy IP>:<proxy port>`<br> 在 PowerShell 中，請使用：`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

目前,AzCopy 不支援需要使用 NTLM 或 Kerberos 進行身份驗證的代理。

## <a name="optimize-performance"></a>效能最佳化

您可以對性能進行基準測試,然後使用命令和環境變數在性能和資源消耗之間找到最佳權衡。

本節可説明您執行以下優化任務:

> [!div class="checklist"]
> * 執行基準測試
> * 輸送量最佳化
> * 優化記憶體使用 
> * 優化檔案同步

### <a name="run-benchmark-tests"></a>執行基準測試

您可以在特定的 Blob 容器上運行性能基準測試,以查看常規性能統計資訊和標識性能瓶頸。 

使用以下命令運行性能基準測試。

|    |     |
|--------|-----------|
| **語法** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **範例** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> 本示例用單個引號 ('') 括括路徑參數。 除 Windows 命令外殼 (cmd.exe) 外,在所有命令 shell 中使用單引號。 如果使用 Windows 命令命令命令命令 (cmd.exe),則用雙引號(")而不是單個引號('')括起來路徑參數。

此命令通過將測試數據上載到指定目標來運行性能基準。 測試數據在記憶體中生成,上傳到目標,然後在測試完成後從目標中刪除。 您可以使用可選擇的指令參數指定要生成的檔案數量以及希望它們的大小。

有關詳細的參考文檔,請參閱[茲貝貝工作臺](storage-ref-azcopy-bench.md)。

要查看此命令的詳細説明指南,請鍵入`azcopy bench -h`然後按 ENTER 鍵。

### <a name="optimize-throughput"></a>輸送量最佳化

您可以使用命令中`cap-mbps`的標誌對輸送量資料速率設置上限。 例如,以下命令將恢復作業並將輸送量限制為`10`每秒兆位 (MB)。 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

傳輸小檔時,輸送量可能會降低。 您可以通過`AZCOPY_CONCURRENCY_VALUE`設置 環境變數來提高輸送量。 此變數指定可能發生的併發請求數。  

如果電腦的 CPU 少於 5 個,則此變數的值`32`將設定為 。 否則,預設值等於 16 乘以 CPU 數。 此變數的最大預設值為`3000`,但您可以手動設置此值更高或更低。 

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

使用`azcopy env`檢查此變數的當前值。 如果該值為空,則可以通過查看任何 AzCopy 日誌檔的開頭來讀取正在使用的值。 所選值及其選擇原因將報告在那裡。

在設置此變數之前,我們建議您運行基準測試。 基準測試過程將報告建議的併發值。 或者,如果網路條件和負載不同,請為此變數設置為單詞`AUTO`,而不是特定數位。 這將導致 AzCopy 始終運行與基準測試中相同的自動調優過程。

### <a name="optimize-memory-use"></a>優化記憶體使用

設置`AZCOPY_BUFFER_GB`環境變數以指定在下載和上傳檔時希望 AzCopy 使用的最大系統記憶體量。
以 GB 為單位表示此值。

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>優化檔案同步

[同步](storage-ref-azcopy-sync.md)命令識別目標中的所有檔案,然後在啟動同步操作之前比較檔名和上次修改的時間戳。 如果您有大量檔,則可以通過消除這種前期處理來提高性能。 

為此,請改用[azcopy 複製](storage-ref-azcopy-copy.md)命令`--overwrite`,並將標誌`ifSourceNewer`設定為 。 AzCopy 將比較文件,因為它們被複製而不執行任何前期掃描和比較。 在需要比較大量文件的情況下,這提供了性能優勢。

[azcopy copy](storage-ref-azcopy-copy.md)指令不會從目標中刪除檔案,因此,如果要在源中不再存在時刪除目標檔案,請使用[azcopy 同步](storage-ref-azcopy-sync.md)指令`--delete-destination``true`,`prompt`將標誌設定為 或 。 

## <a name="troubleshoot-issues"></a>針對問題進行疑難排解

AzCopy 為每個作業創建日誌和計劃檔。 您可以使用記錄來調查任何可能的問題並進行疑難排解。 

日誌將包含失敗狀態`UPLOADFAILED``COPYFAILED`(、`DOWNLOADFAILED`和 ),完整路徑和失敗的原因。

預設情況下,日誌和計劃檔位於 Windows`%USERPROFILE%\.azcopy`上的`$HOME$\.azcopy`目錄中或 Mac 和 Linux 上的目錄中,但您可以更改該位置(如果需要)。

相關錯誤不一定是檔中出現的第一個錯誤。 對於網路錯誤、超時和伺服器忙錯誤等錯誤,AzCopy 將重試多達 20 次,並且重試過程通常成功。  您看到的第一個錯誤可能是成功重試的無害。  因此,不要查看檔中的第一個錯誤,而是查找靠近`UPLOADFAILED``COPYFAILED`或的錯誤`DOWNLOADFAILED`。 

> [!IMPORTANT]
> 向 Microsoft 支援提交請求(或排除涉及任何第三方的問題的疑難排解)時,共用要執行的命令的修訂版本。 這可確保 SAS 不會意外地與任何人共用。 您可以在記錄檔開頭找到編校的版本。

### <a name="review-the-logs-for-errors"></a>檢閱記錄以了解錯誤

以下命令將從`UPLOADFAILED``04dc9ca9-158f-7945-5933-564021086c79`紀錄中取得狀態的所有錯誤:

**視窗(電源外殼)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>檢視和繼續作業

每個傳輸作業都會建立 AzCopy 作業。 使用以下指令檢視工作的歷史紀錄:

```
azcopy jobs list
```

若要檢視作業統計資料，請使用下列命令：

```
azcopy jobs show <job-id>
```

若要依狀態篩選傳輸，請使用下列命令：

```
azcopy jobs show <job-id> --with-status=Failed
```

使用以下命令恢復失敗/已取消的作業。 此命令使用其識別碼和 SAS 權杖,因為它不持久,因為出於安全原因:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> 將路徑參數(如 SAS 令牌)與單引號 ('') 括起來。 除 Windows 命令外殼 (cmd.exe) 外,在所有命令 shell 中使用單引號。 如果使用 Windows 命令命令命令命令 (cmd.exe),則用雙引號(")而不是單個引號('')括起來路徑參數。

恢復作業時,AzCopy 會查看工作計劃檔。 計畫檔列出首次創建作業時識別要處理的所有檔。 恢復作業時,AzCopy 將嘗試傳輸計畫檔中列出的尚未傳輸的所有檔案。

## <a name="change-the-location-of-the-plan-and-log-files"></a>變更排程與紀錄檔的位置

默認情況下,計劃和日誌檔位於 Windows`%USERPROFILE%\.azcopy`上的 目錄中,或者`$HOME$\.azcopy`位於 Mac 和 Linux 上的目錄中。 您可以更改此位置。

### <a name="change-the-location-of-plan-files"></a>變更排程檔案的位置

使用這些命令中的任何一個。

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

使用`azcopy env`檢查此變數的當前值。 如果值為空,則計劃檔將寫入預設位置。

### <a name="change-the-location-of-log-files"></a>變更紀錄檔的位置

使用這些命令中的任何一個。

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

使用`azcopy env`檢查此變數的當前值。 如果值為空,則日誌將寫入預設位置。

## <a name="change-the-default-log-level"></a>變更預設記錄層級

預設情況下,AzCopy 紀錄等級設定為`INFO`。 如果要降低日誌詳細性以節省磁碟空間,請使用``--log-level``選項 覆蓋此設置。 

可用的紀錄等級`NONE``DEBUG``INFO`是: 、、、、、、`FATAL``WARNING``ERROR``PANIC`與 。

## <a name="remove-plan-and-log-files"></a>刪除計畫與紀錄檔

如果要從本地電腦中移除所有計畫與紀錄檔以節省磁碟空間,請使用指令`azcopy jobs clean`。

要刪除僅與一個工作關聯的計畫與紀錄檔,請使用`azcopy jobs rm <job-id>`。 將此範例`<job-id>`中的占位符替換為作業的作業 ID。


