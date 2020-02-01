---
title: 使用 Azure 儲存體設定、優化和疑難排解 AzCopy |Microsoft Docs
description: 設定、優化和疑難排解 AzCopy。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 00ce40e24a01b765419186a609ecf19ce53c772b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905271"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>設定、優化和疑難排解 AzCopy

AzCopy 是命令列公用程式，可讓您在儲存體帳戶之間複製 blob 或檔案。 本文可協助您執行先進的設定工作，並協助您針對使用 AzCopy 時可能發生的問題進行疑難排解。

> [!NOTE]
> 如果您要尋找可協助您開始使用 AzCopy 的內容，請參閱下列文章：
> - [開始使用 AzCopy](storage-use-azcopy-v10.md)
> - [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)
> - [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
> - [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>進行 Proxy 設定

若要設定 AzCopy 的 proxy 設定，請設定 `https_proxy` 環境變數。 如果您在 Windows 上執行 AzCopy，AzCopy 會自動偵測 proxy 設定，因此您不需要在 Windows 中使用此設定。 如果您選擇在 Windows 中使用此設定，將會覆寫自動偵測。

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | 在命令提示字元中，請使用：`set https_proxy=<proxy IP>:<proxy port>`<br> 在 PowerShell 中，請使用：`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

目前，AzCopy 不支援需要使用 NTLM 或 Kerberos 進行驗證的 proxy。

## <a name="optimize-performance"></a>將效能最佳化

您可以基準效能，然後使用命令和環境變數來尋找效能和資源耗用量之間的最佳取捨。

本節可協助您執行這些優化工作：

> [!div class="checklist"]
> * 執行基準測試
> * 輸送量最佳化
> * 優化記憶體使用 
> * 優化檔案同步處理

### <a name="run-benchmark-tests"></a>執行基準測試

您可以在特定的 blob 容器上執行效能基準測試，以查看一般效能統計資料和身分識別效能瓶頸。 

> [!NOTE]
> 在目前版本中，這項功能僅適用于 Blob 儲存體容器。

使用下列命令來執行效能基準測試。

|    |     |
|--------|-----------|
| **語法** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **範例** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> 這個範例會使用單引號（' '）來括住路徑引數。 在所有命令 shell 中使用單引號，但 Windows 命令介面（cmd.exe）除外。 如果您使用 Windows 命令 Shell （cmd.exe），請將路徑引數括在雙引號（""），而不是單引號（' '）。

此命令會將測試資料上傳至指定的目的地，以執行效能基準測試。 測試資料會在記憶體中產生、上傳至目的地，然後在測試完成後從目的地中刪除。 您可以使用選擇性的命令參數，指定要產生的檔案數目，以及您想要的大小。

如需詳細的參考檔，請參閱[azcopy 工作臺](storage-ref-azcopy-bench.md)。

若要查看此命令的詳細說明指引，請輸入 `azcopy bench -h`，然後按 ENTER 鍵。

### <a name="optimize-throughput"></a>輸送量最佳化

您可以使用命令中的 `cap-mbps` 旗標，將輸送量資料速率設為上限。 例如，下列命令會繼續作業，並將每秒的上限輸送量增加到 `10` mb （MB）。 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

傳輸小型檔案時，輸送量可能會降低。 您可以藉由設定 `AZCOPY_CONCURRENCY_VALUE` 環境變數來增加輸送量。 此變數會指定可能發生的並行要求數目。  

如果您的電腦的 Cpu 少於5個，則此變數的值會設定為 `32`。 否則，預設值等於16乘以 Cpu 的數目。 此變數的預設值上限為 `3000`，但您可以手動將此值設定為較高或較低。 

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

請使用 `azcopy env` 來檢查這個變數目前的值。 如果此值為空白，則您可以查看任何 AzCopy 記錄檔的開頭，以讀取所使用的值。 選取的值及其選取的原因會在該處回報。

設定此變數之前，建議您先執行基準測試。 基準測試程式會報告建議的並行值。 或者，如果您的網路條件和承載有所差異，請將此變數設定為單字 `AUTO`，而不是特定的數位。 這會導致 AzCopy 一律執行其在基準測試測試中所使用的相同自動調整程式。

### <a name="optimize-memory-use"></a>優化記憶體使用

設定 `AZCOPY_BUFFER_GB` 環境變數，以指定您想要 AzCopy 在下載和上傳檔案時使用的系統記憶體數量上限。
以 gb 為單位表示此值。

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>優化檔案同步處理

[Sync](storage-ref-azcopy-sync.md)命令會識別目的地上的所有檔案，然後比較檔案名和上次修改的時間戳記，然後再開始同步處理作業。 如果您有大量的檔案，您可以藉由排除這項提前處理來改善效能。 

若要完成此動作，請改用[azcopy copy](storage-ref-azcopy-copy.md)命令，並將 `--overwrite` 旗標設定為 `ifSourceNewer`。 AzCopy 會比較複製的檔案，而不需要執行任何前置掃描和比較。 當有大量檔案要比較時，這會提供效能邊緣。

[Azcopy copy](storage-ref-azcopy-copy.md)命令不會從目的地刪除檔案，因此，如果您想要在目的地上不再存在檔案時刪除檔案中的檔案，請使用[azcopy 同步](storage-ref-azcopy-sync.md)命令，並將 `--delete-destination` 旗標設為 `true` 或 `prompt`的值。 

## <a name="troubleshoot-issues"></a>針對問題進行疑難排解

AzCopy 會為每個作業建立記錄檔和計畫檔案。 您可以使用記錄來調查任何可能的問題並進行疑難排解。 

記錄檔將包含失敗的狀態（`UPLOADFAILED`、`COPYFAILED`和 `DOWNLOADFAILED`）、完整路徑，以及失敗的原因。

根據預設，記錄檔和方案檔位於 Windows 上的 `%USERPROFILE%\.azcopy` 目錄或 Mac 和 Linux 上的 `$HOME$\.azcopy` 目錄，但您可以視需要變更該位置。

> [!IMPORTANT]
> 提交要求以 Microsoft 支援服務（或針對任何協力廠商的問題進行疑難排解）時，請共用您要執行之命令的編校版本。 這可確保 SAS 不會意外與任何人共用。 您可以在記錄檔開頭找到編校的版本。

### <a name="review-the-logs-for-errors"></a>檢閱記錄以了解錯誤

下列命令會從 `04dc9ca9-158f-7945-5933-564021086c79` 記錄檔取得具有 `UPLOADFAILED` 狀態的所有錯誤：

**Windows （PowerShell）**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>檢視和繼續作業

每個傳輸作業都會建立 AzCopy 作業。 使用下列命令來查看工作的歷程記錄：

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

使用下列命令來繼續失敗/已取消的工作。 此命令會使用其識別碼和 SAS 權杖，因為它在安全性方面並不會持續：

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> 將路徑引數（例如 SAS 權杖）括在單引號（' '）。 在所有命令 shell 中使用單引號，但 Windows 命令介面（cmd.exe）除外。 如果您使用 Windows 命令 Shell （cmd.exe），請將路徑引數括在雙引號（""），而不是單引號（' '）。

當您繼續工作時，AzCopy 會查看作業計畫檔案。 計畫檔案會列出第一次建立作業時識別要處理的所有檔案。 當您繼續工作時，AzCopy 會嘗試傳送尚未傳送的計畫檔案中列出的所有檔案。

## <a name="change-the-location-of-the-plan-and-log-files"></a>變更計畫和記錄檔的位置

根據預設，方案和記錄檔位於 Windows 上的 `%USERPROFILE%\.azcopy` 目錄，或 Mac 和 Linux 上的 `$HOME$\.azcopy` 目錄。 您可以變更此位置。

### <a name="change-the-location-of-plan-files"></a>變更計畫檔案的位置

使用任何這些命令。

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

請使用 `azcopy env` 來檢查這個變數目前的值。 如果此值為空白，則會將計畫檔案寫入至預設位置。

### <a name="change-the-location-of-log-files"></a>變更記錄檔的位置

使用任何這些命令。

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

請使用 `azcopy env` 來檢查這個變數目前的值。 如果此值為空白，則會將記錄寫入至預設位置。

## <a name="change-the-default-log-level"></a>變更預設記錄層級

根據預設，AzCopy 記錄層級會設定為 `INFO`。 如果您想要減少記錄的詳細資訊以節省磁碟空間，請使用 [``--log-level``] 選項來覆寫此設定。 

可用的記錄層級為： `NONE`、`DEBUG`、`INFO`、`WARNING`、`ERROR`、`PANIC`和 `FATAL`。

## <a name="remove-plan-and-log-files"></a>移除方案和記錄檔

如果您想要從本機電腦移除所有的計畫和記錄檔，以節省磁碟空間，請使用 `azcopy jobs clean` 命令。

若只要移除與一項作業相關聯的方案和記錄檔，請使用 `azcopy jobs rm <job-id>`。 將此範例中的 `<job-id>` 預留位置取代為作業的作業識別碼。


